# Room: Windows Threat Detection 2

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how attackers perform post-compromise discovery, collect sensitive information, transfer additional tools, and prepare stolen data for exfiltration. Understand how SOC analysts detect these activities using Windows logs and Sysmon.

---

# Topics Covered

* Discovery
* Collection
* Credential Access
* Exfiltration
* Ingress Tool Transfer
* Sysmon Detection
* Threat Hunting

---

# Key Concepts

## Discovery

After gaining initial access, attackers begin gathering information about the compromised system.

Common discovery objectives include:

* Files and folders
* Local users and groups
* Installed applications
* Running services
* Network configuration
* Antivirus solutions

Typical commands include:

* `whoami`
* `systeminfo`
* `ipconfig /all`
* `net user`
* `net localgroup`
* `tasklist`
* `Get-LocalUser`
* `Get-Service`

These commands help attackers understand the environment before continuing the attack.

---

## Discovery via GUI

Interactive attackers, especially after an RDP compromise, may use graphical Windows tools instead of command-line utilities.

Examples include:

* File Explorer
* Control Panel
* Event Viewer
* Computer Management
* Programs and Features

SOC analysts should investigate unusual process trees created by these applications.

---

## Collection

Once attackers understand the environment, they begin collecting valuable information.

Common targets include:

* Documents
* PDF files
* Password files
* Browser data
* Chat history
* Configuration files

Attackers often search, copy, and archive these files before exfiltration.

---

## Detecting Collection

Indicators of collection activity include:

* Searching for sensitive files
* Copying files into temporary directories
* Compressing files into ZIP archives
* Searching for keywords such as **password**
* Accessing large numbers of user documents

These activities can be correlated using Sysmon Process Creation and File Creation events.

---

## Ingress Tool Transfer

Attackers rarely bring all required tools during the initial compromise.

Instead, they download additional tools when needed.

Common examples include:

* Seatbelt
* Mimikatz
* Remote Access Trojans (RATs)
* Ransomware

---

## Common Transfer Methods

Frequently used download methods include:

* `certutil`
* `curl`
* PowerShell `Invoke-WebRequest`
* Web browsers
* RDP file transfer

Monitoring these utilities is important because they are frequently abused by attackers.

---

## Exfiltration

The final objective is transferring stolen information outside the organization.

Before exfiltration, attackers commonly:

* Compress collected files
* Move them into staging directories
* Upload them to external servers

Large outbound transfers or unusual archive creation should always be investigated.

---

# Key Takeaways

* Discovery usually begins immediately after Initial Access.
* Attackers use built-in Windows commands to understand the environment.
* Collection focuses on acquiring valuable data rather than system information.
* Attackers frequently download additional tools after compromising a system.
* Sysmon provides valuable telemetry for detecting discovery, collection, and malware downloads.
* Correlating process execution, file creation, and network activity helps reconstruct attacker behavior.

---

# SOC Perspective

SOC analysts should monitor for suspicious discovery commands, abnormal process trees, archive creation, execution of built-in download utilities such as **certutil**, **curl**, and **PowerShell**, as well as unusual outbound network connections. Correlating Sysmon events with Windows Security Logs enables analysts to identify attacker activity before data exfiltration or ransomware deployment occurs.
