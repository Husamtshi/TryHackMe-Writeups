# Room: Alert Triage with Elastic

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how to investigate security alerts using **Elastic Security (ELK Stack)** by validating alerts, correlating events across multiple log sources, building attack timelines, and determining whether alerts are **True Positives** or **False Positives**.

---

# Topics Covered

- Elastic Security
- Alert Triage
- Web Attack Investigation
- User Activity Analysis
- Command Execution
- Timeline Analysis
- Event Correlation
- Process Investigation
- Authentication Analysis
- Incident Escalation

---

# What is Alert Triage?

Alert triage is the process of validating security alerts to determine whether they represent a real security incident.

The analyst's goal is **not** simply to acknowledge an alert, but to answer questions such as:

- Is the alert legitimate?
- Is it malicious?
- Who performed the activity?
- Which host was affected?
- What happened before and after the alert?
- What is the impact?
- Should the incident be escalated?

Every investigation starts with collecting evidence before making a decision.

---

# General Investigation Workflow

A structured investigation usually follows this order:

```
Alert
↓
Review Alert Details
↓
Identify User
↓
Identify Host
↓
Search Related Events
↓
Build Timeline
↓
Correlate Logs
↓
Determine Impact
↓
True Positive / False Positive
↓
Escalate if Necessary
```

Rather than relying on a single event, analysts correlate multiple pieces of evidence to understand the full attack.

---

# Scenario 1 — Investigating Web Attacks

The first investigation focuses on suspicious web server activity.

Before searching in Elastic, review:

- Alert Time
- Source IP
- Destination Host
- Requested URL
- HTTP Method
- Response Code

Common web attacks include:

- Directory Enumeration
- SQL Injection
- Command Injection
- Web Shell Activity
- File Upload Attempts
- Exploitation Attempts

Useful investigation questions:

- Which URI was requested?
- How many requests were made?
- Which IP generated them?
- Were multiple endpoints targeted?
- Did any request succeed?
- Was a suspicious User-Agent used?

Indicators of malicious activity include:

- Large request volumes
- Automated scanners
- Suspicious URI patterns
- Repeated HTTP 404/403 responses
- Access to administrative pages
- Attempts to upload or execute files

Elastic makes it easy to pivot between:

- Source IP
- Destination Host
- URI
- User-Agent
- Response Codes
- Timeline

to determine whether the activity is reconnaissance or an active attack.

---

# Scenario 2 — Uncovering Account Activity

The second scenario investigates user authentication.

Analysts should first review:

- Username
- Host
- Login Time
- Source IP
- Authentication Result

Questions to answer:

- Is the login normal?
- Has this user logged in from this location before?
- Did multiple failed logins occur?
- Was privilege escalation attempted?
- Did the user execute unusual commands?

Typical investigation pivots include:

- Previous logins
- Authentication failures
- Account lockouts
- New logins
- Password resets
- Administrative actions

Timeline analysis is critical because account compromise usually consists of several related events rather than a single alert.

---

# Scenario 3 — Exposing Command Execution

The final scenario focuses on process execution.

Attackers often execute commands after gaining access to a system.

Common objectives include:

- Discovery
- Privilege Escalation
- Persistence
- Credential Access
- Malware Execution

Typical investigation steps:

Review:

- Process Name
- Parent Process
- Command Line
- User
- Host
- Execution Time

Questions to ask:

- Who started the process?
- Was it expected?
- Was PowerShell involved?
- Was cmd.exe abused?
- Were LOLBins executed?
- Did network connections follow?

Building the process chain helps determine whether the execution was legitimate administration or malicious activity.

---

# Timeline Analysis

Elastic allows analysts to reconstruct an attack chronologically.

Example:

```
User Login
↓
PowerShell Execution
↓
Network Connection
↓
Suspicious Process
↓
Persistence
↓
Additional Commands
```

This timeline provides context that isolated events cannot.

---

# Event Correlation

Elastic excels at correlating events across different data sources.

Common correlations include:

```
Authentication
↓
Process Creation
↓
Network Connections
↓
File Creation
↓
Registry Changes
↓
Alerts
```

Rather than treating alerts independently, analysts investigate how events relate to each other.

---

# True Positive vs False Positive

The purpose of triage is deciding whether the alert represents real malicious activity.

## True Positive

Evidence confirms malicious behaviour.

Examples:

- Successful compromise
- Malicious PowerShell
- Web exploitation
- Malware execution
- Persistence

Action:

Escalate to SOC L2 / Incident Response.

---

## False Positive

The activity is legitimate.

Examples:

- Administrator maintenance
- Vulnerability scanner
- Approved software
- Scheduled automation

Action:

Document findings and close the alert.

---

# Investigation Best Practices

- Never trust the alert alone.
- Always validate with additional logs.
- Build a timeline before drawing conclusions.
- Investigate the user and the affected host.
- Review parent and child processes.
- Correlate authentication, process, and network activity.
- Collect evidence before escalating.
- Document every conclusion.

---

# Key Takeaways

- Alert triage focuses on validating alerts rather than trusting them blindly.
- Elastic Security allows analysts to investigate events from multiple log sources in one platform.
- Effective investigations rely on timelines and event correlation.
- User, host, process, and network activity should always be investigated together.
- Process execution often provides the strongest evidence of attacker activity.
- Web attack investigations focus on requests, URIs, User-Agents, and response codes.
- Authentication analysis helps detect compromised accounts and suspicious logins.
- The analyst's final responsibility is determining whether an alert is a **True Positive** or **False Positive** and escalating incidents when appropriate.

---

# SOC Perspective

Alert triage is one of the most important responsibilities of a SOC analyst. Rather than reacting to alerts immediately, analysts gather evidence, correlate events, reconstruct the attack timeline, and evaluate the overall context. **Elastic Security** provides a centralized view of endpoint, authentication, and network telemetry, enabling analysts to distinguish normal administrative activity from genuine attacks and make informed escalation decisions.
