# Room: Alert Triage with Splunk

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how to investigate security alerts using **Splunk SIEM** by validating alerts, correlating events across multiple log sources, building attack timelines, and determining whether an alert is a **True Positive** or **False Positive**.

---

# Topics Covered

- Splunk SIEM
- Alert Triage
- Linux SSH Brute Force Investigation
- Windows Scheduled Task Analysis
- Web Shell Investigation
- Event Correlation
- Timeline Analysis
- Authentication Logs
- Process Investigation
- Incident Escalation

---

# What is Alert Triage?

Alert triage is the process of validating security alerts before deciding whether they represent malicious activity.

The objective is to answer questions such as:

- Is the alert legitimate?
- Which host is affected?
- Which user performed the activity?
- What happened before and after the alert?
- Is this a True Positive or False Positive?
- Should the incident be escalated?

Rather than trusting the alert immediately, analysts gather evidence from multiple log sources.

---

# Alert Triage Methodology

Before opening Splunk, always review the alert itself.

Questions to answer first:

- What type of alert is it?
- Which host is affected?
- Which user is involved?
- What is the source IP?
- What is the timestamp?
- Is the asset a workstation or a server?
- Does the activity make sense for this user?

Only after understanding the context should you begin investigating logs.

---

# Scenario 1 – Linux SSH Brute Force

## Alert Details

- **Alert Name:** Brute Force Activity Detection
- **Target Host:** tryhackme-2404
- **Source IP:** 10.10.242.248
- **Log Source:** `linux-alert`

---

## Initial Analysis

The alert shows:

- Internal source IP
- Linux server
- Activity during business hours

The internal IP suggests the attacker may already have access to the internal network or VPN.

---

## Step 1 – Review Authentication Events

Search for SSH authentication attempts.

```spl
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| search "Accepted password for" OR "Failed password for" OR "Invalid user"
| sort + _time
```

Look for:

- Failed passwords
- Successful logins
- Invalid usernames

The presence of many **Invalid user** events often indicates username enumeration before a brute-force attack.

---

## Step 2 – Count Attempts Per User

Extract usernames and calculate login attempts.

```spl
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| rex field=_raw "^\d{4}-\d{2}-\d{2}T[^\s]+\s+(?<log_hostname>\S+)"
| rex field=_raw "sshd\[\d+\]:\s*(?<action>Failed|Accepted)\s+\S+\s+for(?: invalid user)? (?<username>\S+) from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| eval process="sshd"
| stats count values(src_ip) as src_ip values(log_hostname) as hostname values(process) as process by username
```

Result:

- Four users targeted.
- **john.smith** received **503** authentication attempts.

This strongly indicates brute-force activity.

---

## Step 3 – Verify Successful Login

Determine whether the attacker successfully authenticated.

```spl
index="linux-alert" sourcetype="linux_secure" 10.10.242.248
| rex field=_raw "^\d{4}-\d{2}-\d{2}T[^\s]+\s+(?<log_hostname>\S+)"
| rex field=_raw "sshd\[\d+\]:\s*(?<action>Failed|Accepted)\s+\S+\s+for(?: invalid user)? (?<username>\S+) from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| eval process="sshd"
| stats count values(action) values(src_ip) as src_ip values(log_hostname) as hostname values(process) as process by username
```

Result:

- Hundreds of failed logins.
- One successful login for **john.smith**.

---

## Investigation Conclusion

Classification:

✅ **True Positive**

Evidence:

- Username enumeration.
- Hundreds of failed login attempts.
- Successful authentication afterward.

Recommended actions:

- Escalate to SOC L2.
- Notify the Incident Response team.
- Reset compromised credentials.
- Investigate post-authentication activity.
- Review lateral movement and persistence.

---

# Scenario 2 – Windows Scheduled Task Investigation

## Alert Details

- Windows Scheduled Task Created
- Event ID **4698**
- Task Name: **AssessmentTaskOne**

---

## Initial Investigation

Never begin directly with Splunk.

Start by understanding the alert context.

### Host

Determine whether the asset is:

- Workstation
- Server

Naming conventions often help.

Examples:

- SRV
- WEB
- SQL
- WIN
- HOST

Example:

```
WIN-H015
```

→ Workstation

---

### User Context

Review:

- Department
- Role
- Working hours
- Geographic location

Example:

```
Oliver Thompson
System Engineer
```

A System Engineer creating scheduled tasks is much less suspicious than an HR employee doing the same.

Context is critical before drawing conclusions.

---

## Search Task Creation

```spl
index="win-alert" EventCode=4698 AssessmentTaskOne
| table _time EventCode user_name host Task_Name Message
```

This confirms:

- Task name
- User
- Host
- Creation time

---

## Continue the Investigation

Finding Event ID **4698** is only the beginning.

Investigate:

- Sysmon Event ID 1 (Process Creation)
- Windows Event ID 4688
- Parent process
- PowerShell execution
- CMD execution
- Network connections
- File creation
- Registry modifications
- Scheduled task execution

Questions to answer:

- Who created the task?
- Why was it created?
- What executable does it launch?
- Does it establish persistence?
- Is it part of legitimate administration?

---

# Scenario 3 – Web Shell Investigation

A web shell allows attackers to execute commands remotely through a compromised web server.

Common web shell extensions include:

- .php
- .asp
- .aspx
- .jsp

---

## Common Indicators

Look for:

- Multiple POST requests
- Suspicious GET requests
- Access to newly uploaded scripts
- HTTP 200 responses
- Unusual User-Agent values
- Repeated access to the same script

---

## Example Splunk Search

```spl
index=*
status=200
(method=GET OR method=POST)
uri_path IN (*.php, *.asp, *.aspx, *.jsp)
| stats values(clientip) values(useragent) values(uri) count by referer_domain
| where count > 2
```

---

## Investigation Checklist

- Identify the suspicious file.
- Review upload activity.
- Check web server access logs.
- Review the source IP.
- Analyze the User-Agent.
- Correlate with process creation logs.
- Look for persistence mechanisms.
- Investigate possible lateral movement.

---

# Typical SOC Investigation Workflow

```
Alert
↓

Review Alert Details
↓

Identify User

↓

Identify Host

↓

Search Related Logs

↓

Build Timeline

↓

Correlate Events

↓

Identify Indicators of Compromise (IOCs)

↓

Determine:
• True Positive
• False Positive

↓

Escalate if Required
```

Every investigation should follow a structured methodology instead of relying on a single log entry.

---

# Best Practices

- Never investigate logs without first understanding the alert context.
- Always gather host and user information before searching.
- Correlate multiple log sources.
- Build a complete attack timeline.
- Validate every alert with supporting evidence.
- Investigate parent and child processes.
- Base conclusions on behaviour rather than a single indicator.

---

# Key Takeaways

- Alert triage is about validating alerts before escalating incidents.
- Context (host, user, role, and timing) is as important as the logs themselves.
- Username enumeration is often the first stage of an SSH brute-force attack.
- A successful login after hundreds of failed attempts strongly indicates account compromise.
- Event ID 4698 confirms scheduled task creation but requires further investigation to determine intent.
- Web shell investigations should combine web logs, process creation, and network activity.
- Effective SOC investigations rely on event correlation rather than isolated alerts.

---

# SOC Perspective

A SOC analyst's responsibility is not simply to respond to alerts, but to determine their legitimacy through evidence-based investigation. Splunk provides the ability to correlate authentication logs, process creation, scheduled tasks, web activity, and network events into a complete attack timeline. A skilled analyst focuses on context, validates every indicator, and escalates only when sufficient evidence confirms malicious activity.
