# Room: Windows Logging for SOC

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how SOC analysts use Windows Security Logs and Sysmon to investigate authentication events, process execution, user account changes, and PowerShell activity in order to detect malicious behavior and reconstruct attack timelines.

---

# Topics Covered

* Windows Security Logs
* Authentication Events
* User Management Events
* Sysmon Process Monitoring
* Process Correlation
* PowerShell Logging
* Threat Hunting

---

# Key Concepts

## Authentication Events

The most important Windows Security Event IDs are:

* **4624** – Successful Logon
* **4625** – Failed Logon

These events help analysts detect:

* RDP logins
* Brute-force attacks
* Password spraying
* Suspicious remote access

When investigating successful logins, the **Logon ID** is essential because it uniquely identifies the user's session and allows correlation with other events.

---

## Hunting RDP Attacks

When reviewing RDP activity, analysts should look for:

* Logon Type **10** (RDP)
* Logon Type **3** (Network authentication when NLA is enabled)
* Unknown workstation names
* Suspicious source IP addresses
* Failed logins followed by a successful authentication

These indicators may suggest brute-force attacks or unauthorized remote access.

---

## User Management Events

Important Event IDs include:

* **4720** – User Account Created
* **4722** – User Account Enabled
* **4723 / 4724** – Password Changed or Reset
* **4725 / 4726** – Account Disabled or Deleted
* **4732 / 4733** – User Added to or Removed from a Security Group
* **4738** – User Account Changed

These events help detect privilege escalation, backdoor accounts, unauthorized password resets, and suspicious administrative activity.

---

## Sysmon Process Monitoring

Sysmon Event ID **1 (Process Creation)** provides detailed visibility into:

* Process name
* Command line
* Parent process
* Process hash
* User context
* Logon ID

Compared to Windows Event ID 4688, Sysmon provides significantly more useful information for investigations.

---

## Process Investigation

When analyzing process activity, common red flags include:

* Executables launched from unusual directories such as `C:\Temp` or `C:\Users\Public`
* Random or suspicious executable names
* Parent-child process relationships that do not make sense
* Malicious file hashes identified through VirusTotal

Following the parent process chain helps reconstruct the attack sequence.

---

## File, Registry and Network Activity

Sysmon can also monitor:

* **Event ID 3** – Network Connections
* **Event ID 11** – File Creation
* **Event ID 13** – Registry Value Modification
* **Event ID 22** – DNS Queries

These events help identify:

* Malware communication
* Persistence mechanisms
* Malicious file creation
* Connections to suspicious domains or external IP addresses

---

## PowerShell Logging

PowerShell is commonly abused by attackers because it allows multiple actions within a single process.

Unlike normal process creation logs, PowerShell logging captures command history, making it useful for detecting:

* System discovery
* Malware download
* Data exfiltration
* Administrative abuse

PowerShell history remains available across system reboots unless manually deleted.

---

# Key Takeaways

* Event IDs **4624** and **4625** are fundamental for authentication investigations.
* Logon ID is essential for correlating Windows events.
* Sysmon provides richer telemetry than default Windows Security Logs.
* Process trees help analysts understand attacker behavior.
* File, Registry, DNS, and Network events reveal persistence and malware activity.
* PowerShell logging is a critical source during modern incident investigations.

---

# SOC Perspective

Windows Security Logs and Sysmon are among the most valuable telemetry sources for SOC analysts. By correlating Logon IDs, Process IDs, authentication events, process creation, registry changes, DNS activity, and PowerShell logs, analysts can reconstruct attack chains, identify malicious behavior, and respond to security incidents more effectively.
