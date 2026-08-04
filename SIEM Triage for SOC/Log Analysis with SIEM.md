# Room: Log Analysis with SIEM

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how Security Information and Event Management (SIEM) platforms collect, normalize, correlate, and analyze logs from multiple sources to detect security incidents. The room focuses on using SIEM (Splunk examples) to investigate Windows, Linux, and Web-based attacks.

---

# Topics Covered

- SIEM Fundamentals
- Log Centralization
- Event Correlation
- Historical Analysis
- Log Sources
- Time Synchronization
- Log Normalization
- Windows Security Logs
- Sysmon
- Linux Logs
- Web Server Logs
- Splunk Searches
- Detection Queries

---

# What is a SIEM?

A **Security Information and Event Management (SIEM)** platform collects logs from multiple systems into one centralized location where analysts can search, correlate, investigate, and respond to security events.

Instead of checking logs separately on:

- Windows Servers
- Linux Servers
- Firewalls
- IDS / IPS
- Cloud Platforms
- Web Servers
- Active Directory

everything becomes searchable from one interface.

Popular SIEM solutions include:

- Splunk
- Microsoft Sentinel
- QRadar
- Elastic Security
- ArcSight

---

# Why SIEM is Important

## 1. Centralization

Without SIEM:

```
Windows Logs
↓

Linux Logs
↓

Firewall

↓

Web Server

↓

Cloud Logs
```

Each system must be investigated individually.

With SIEM:

```
All Logs
↓

Single Platform
↓

Single Search
↓

Single Timeline
```

Benefits:

- Faster investigations
- Easier searching
- Better visibility
- Less context switching

---

## 2. Correlation

Correlation is one of the most powerful SIEM capabilities.

Instead of treating every alert independently, SIEM links related events together.

Example:

```
IDS Alert
↓

Internal Port Scan
↓

Source IP

↓

Sysmon

↓

Process Creation

↓

Windows Security Log

↓

User Account

↓

Complete Attack Story
```

Rather than seeing isolated alerts, analysts reconstruct the full attack chain.

---

## 3. Historical Analysis

SIEM stores historical logs.

This allows analysts to answer questions like:

- Has this user logged in from this country before?
- Has this IP communicated with us previously?
- Did this process execute last week?
- When did the attack begin?

Historical data is critical for:

- Threat Hunting
- Incident Response
- Timeline Creation
- Behavior Analysis

---

# Common Log Sources

SIEM ingests logs from many different systems.

---

## Host-Based Logs

Collected from endpoints and servers.

Examples:

- Windows Event Logs
- Sysmon
- Linux auth.log
- Linux syslog
- EDR Logs

Used for detecting:

- Logins
- Processes
- Privilege Escalation
- Persistence
- Malware

---

## Network-Based Logs

Collected from network devices.

Examples:

- Firewall
- IDS
- IPS
- Router
- Switch
- VPN

Used for detecting:

- Port Scans
- Network Connections
- DDoS
- Brute Force
- Lateral Movement

---

## Web-Based Logs

Collected from:

- Apache
- Nginx
- IIS

Useful for detecting:

- Web Attacks
- SQL Injection
- Web Shells
- Brute Force
- Directory Enumeration
- Exploitation Attempts

---

## Additional Sources

Modern SIEMs also collect logs from:

- Microsoft Entra ID
- AWS
- Azure
- Google Cloud
- Office 365
- Third-party applications

---

# Time Pitfalls

One common challenge during investigations is time.

Different systems may log events using different time zones.

Examples:

```
Windows
UTC

Linux
UTC+2

Firewall
Local Time

Cloud
UTC
```

SIEM usually normalizes timestamps, but analysts must always verify:

- SIEM timezone
- Original log timezone
- Local timezone

Incorrect assumptions about timestamps can lead to incorrect timelines.

---

# Log Normalization

Every product generates logs differently.

Examples:

```
JSON

XML

CSV

Plain Text
```

Field names also vary.

Example:

```
Windows

EventID
```

```
Sysmon

EventCode
```

```
Firewall

Action
```

Normalization converts different formats into a unified structure.

Benefits:

- Easier searching
- Easier filtering
- Easier correlation
- Consistent field names

Without normalization, every product would require different queries.

---

# Windows Analysis

## Sysmon

Sysmon provides detailed endpoint telemetry beyond standard Windows logs.

Commonly investigated events include:

| Event ID | Purpose |
|----------|----------|
| 1 | Process Creation |
| 3 | Network Connection |
| 7 | Image Loaded |
| 11 | File Created |
| 13 | Registry Modification |

Sysmon is one of the most valuable Windows log sources for SOC analysts.

---

## Detecting Suspicious PowerShell

Example indicators:

- EncodedCommand
- IEX
- Invoke-WebRequest
- DownloadString
- ExecutionPolicy Bypass

Typical Sysmon query searches:

- Event ID 1
- powershell.exe
- Suspicious command-line arguments

Goal:

Identify malicious PowerShell execution before malware deployment.

---

## Detecting Suspicious Network Connections

Sysmon Event ID 3 logs outbound connections.

Useful fields:

- Destination IP
- Destination Port
- Process
- User
- Host

Typical red flags:

- Unknown external IPs
- C2 traffic
- Port 4444
- Unusual outbound HTTPS
- Beaconing behavior

---

# Windows Security Logs

Security Logs remain one of the primary investigation sources.

Common activities include:

- Authentication
- User Creation
- Password Reset
- Group Membership Changes
- Process Execution
- Policy Changes
- Audit Changes

Frequently investigated Event IDs include:

- 4624
- 4625
- 4688
- 4720
- 4724
- 4732
- 4698

---

# Linux Analysis

Two primary Linux log sources:

---

## auth.log

Contains:

- SSH Logins
- Failed Logins
- sudo
- su
- Authentication Events

Useful for:

- Brute Force
- SSH Compromise
- Privilege Escalation
- User Activity

Example investigation:

```spl
index=linux source="auth.log" *ubuntu* process=sshd
| search "Accepted password" OR "Failed password"
```

---

## Detecting Privilege Escalation

Attackers often execute:

- sudo
- su

Search example:

```spl
index=linux source="auth.log" *su*
| sort + _time
```

Review:

- User
- Timestamp
- Source
- Parent Activity

---

## syslog

General Linux system events.

Useful for:

- Service Restarts
- Cron Jobs
- Background Processes
- Persistence
- System Changes

---

## Detecting Persistence

Attackers frequently abuse cron jobs.

Example query:

```spl
index=linux sourcetype=syslog ("CRON" OR "cron")
| search ("python" OR "perl" OR "ruby" OR ".sh" OR "bash" OR "nc")
```

Suspicious indicators:

- Reverse Shells
- Bash Scripts
- Python Payloads
- Netcat
- Persistence Scripts

---

# Web Log Analysis

Web server logs are essential for detecting attacks against public applications.

Common investigations include:

- Brute Force
- Web Shells
- DDoS
- Scanning
- Exploitation Attempts

---

# Detecting WordPress Brute Force

Characteristics:

- POST requests
- /wp-login.php
- High request count
- Same IP
- Short time window

Example query:

```spl
index=* method=POST uri_path="/wp-login.php"
| bin _time span=5m
| stats values(referer_domain) as referer_domain values(status) as status values(useragent) as UserAgent values(uri_path) as uri_path count by clientip _time
| where count > 25
```

Red Flags:

- Hundreds of POST requests
- Repeated login attempts
- Single attacking IP
- Automated User-Agent

---

# Detecting Possible Web Shells

Common web shell extensions:

- .php
- .phtml
- .asp
- .aspx
- .jsp
- .exe

Indicators:

- GET requests
- POST requests
- HTTP 200
- Repeated access
- Suspicious parameters

Example query:

```spl
index=*
| search status=200 AND uri_path IN(*.php, *.phtm, *.asp, *.aspx, *.jsp, *.exe)
| stats values(status) as status values(useragent) as UserAgent values(method) as method values(uri) as uri values(clientip) as clientip count by referer_domain
| where count > 2
```

Investigation should include:

- Client IP
- User-Agent
- Requested URI
- Request Frequency

---

# Detecting DDoS

A common indicator:

HTTP Status:

```
503 Service Unavailable
```

Often caused by:

- Server overload
- Massive request volume

Example query:

```spl
index=* status=503
| bin _time span=10m
| stats values(referer_domain) as referer_domain values(status) as status values(useragent) as UserAgent values(uri_path) as uri_path count by clientip _time
| where count > 100000
```

Review:

- Client IP
- Target URI
- User-Agent
- Request Count

---

# Typical SOC Investigation Workflow

```
Alert
↓

Identify Log Source
↓

Search SIEM
↓

Correlate Events
↓

Build Timeline
↓

Identify User
↓

Identify Host

↓

Identify Process

↓

Review Network Activity

↓

Determine Impact

↓

Containment

↓

Escalation
```

Rather than looking at isolated alerts, analysts correlate multiple data sources to understand the complete attack.

---

# SIEM Best Practices

- Always correlate multiple log sources.
- Verify timestamps and time zones.
- Understand normalized fields.
- Use process trees whenever possible.
- Review historical events before concluding.
- Validate alerts using endpoint, network, and web logs together.
- Investigate context rather than relying on a single indicator.

---

# Key Takeaways

- SIEM centralizes security logs from multiple sources into a single platform.
- Correlation links isolated events into complete attack chains.
- Historical logs help identify attacker behavior over time.
- Log normalization makes different log formats searchable through a common structure.
- Sysmon provides rich endpoint visibility for Windows investigations.
- Windows Security Logs remain essential for authentication and account investigations.
- Linux auth.log and syslog provide visibility into authentication, privilege escalation, and persistence.
- Web server logs help detect brute force attacks, web shells, DDoS attacks, and exploitation attempts.
- Effective SOC investigations rely on combining endpoint, network, and web telemetry rather than analyzing alerts in isolation.

---

# SOC Perspective

A SIEM is the central investigation platform within a Security Operations Center. Its greatest strength is not simply collecting logs, but correlating events across multiple systems to reconstruct the attack lifecycle. A skilled SOC analyst uses SIEM to pivot between Windows, Linux, network, cloud, and web telemetry, building a timeline that transforms isolated alerts into a complete understanding of attacker behavior.
