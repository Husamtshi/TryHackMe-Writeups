# Alert Triage with Splunk

## Overview

This room focuses on the daily responsibilities of a SOC Level 1 analyst when investigating alerts inside a SIEM. The goal is not simply to search logs, but to validate alerts, build context, determine whether the activity is malicious, and decide whether escalation is required.

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
- Activity occurred during business hours

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

The presence of many **Invalid user** events often indicates username enumeration before brute force.

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

Reason:

- Username enumeration
- Hundreds of failed logins
- Successful authentication afterward

Recommended actions:

- Escalate to SOC L2.
- Notify Incident Response.
- Reset compromised credentials.
- Investigate post-authentication activity.

---

# Scenario 2 – Scheduled Task Investigation

## Alert Details

- Windows Scheduled Task Created
- Event ID **4698**
- Task Name: **AssessmentTaskOne**

---

## Investigation Strategy

Never begin with Splunk.

Start by understanding:

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

Understand:

- Department
- Role
- Working hours
- Location

Example:

```
Oliver Thompson
System Engineer
```

A System Engineer creating scheduled tasks is less suspicious than an HR employee doing the same.

Context is everything.

---

## Search Task Creation

```spl
index="win-alert" EventCode=4698 AssessmentTaskOne
| table _time EventCode user_name host Task_Name Message
```

This confirms:

- Task name
- Host
- User
- Creation time

---

## Continue the Investigation

Finding Event ID **4698** is only the beginning.

Investigate:

- Sysmon Event ID 1 (Process Creation)
- Event ID 4688
- Parent Process
- PowerShell execution
- CMD execution
- Network connections
- File creation
- Registry changes
- Scheduled task execution

Determine:

- Who created the task?
- Why?
- Is the task legitimate?
- What executable does it launch?
- Does it establish persistence?

---

# Scenario 3 – Web Shell Alert

A web shell allows attackers to execute commands remotely through a compromised web server.

Common file types:

- .php
- .asp
- .aspx
- .jsp

---

## Indicators

Look for:

- Multiple POST requests
- Suspicious GET requests
- Access to newly uploaded scripts
- Requests returning HTTP 200
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
- Review source IP.
- Review User-Agent.
- Correlate with process creation logs.
- Check for persistence.
- Investigate possible lateral movement.

---

# SOC Investigation Workflow

Every investigation should follow a structured methodology:

1. Review the alert.
2. Gather host and user context.
3. Search related logs.
4. Build a timeline.
5. Correlate multiple log sources.
6. Identify Indicators of Compromise (IOCs).
7. Determine:
   - True Positive
   - False Positive
8. Escalate when evidence confirms malicious activity.

---

# Key Takeaways

- Never investigate logs without first understanding the alert context.
- Context (host, user, role, timing) is as important as log analysis.
- Correlation between multiple log sources is essential.
- Username enumeration often precedes brute-force attacks.
- A successful login after many failures usually confirms compromise.
- Event ID 4698 indicates scheduled task creation but requires additional investigation.
- Web shell alerts should always be correlated with web logs, process creation, and network activity.
- SOC analysts should base decisions on evidence, not assumptions.

---

# SOC Analyst Notes

- Always build context before searching.
- Correlate multiple log sources.
- Build a timeline.
- Investigate parent and child processes.
- Validate alerts before escalating.
- Escalate with evidence, not assumptions.
- Focus on behaviour, not only signatures.
