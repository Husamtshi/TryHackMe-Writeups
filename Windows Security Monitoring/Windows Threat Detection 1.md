# Room: Windows Threat Detection 1

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how attackers gain initial access to Windows systems, understand common attack vectors, and identify Windows artifacts that help SOC analysts detect phishing, exposed services, malicious downloads, and infected USB devices.

---

# Topics Covered

* Initial Access
* Exposed Services
* Phishing
* Malicious Attachments
* LNK Files
* Removable Media
* Sysmon Detection
* Threat Hunting

---

# Key Concepts

## Initial Access

Initial Access is the first stage of an attack where the adversary gains entry to a target system.

Common Windows attack vectors fall into two categories:

* Exposed Services
* User-Driven Attacks

---

## Exposed Services

Internet-facing services increase the attack surface and are frequently targeted by automated scanners.

Common examples include:

* RDP
* SSH
* Web Applications
* Mail Servers

Common MITRE ATT&CK techniques:

* **T1133** – External Remote Services
* **T1190** – Exploit Public-Facing Application

Weak passwords, exposed RDP, and unpatched vulnerabilities are common causes of compromise.

---

## User-Driven Attacks

Many Windows compromises rely on user interaction rather than technical vulnerabilities.

Common attack methods include:

* Phishing emails
* Malicious attachments
* Fake software
* Infected USB devices

Common MITRE ATT&CK techniques:

* **T1566** – Phishing
* **T1091** – Removable Media

---

## Risks of Exposed RDP

Remote Desktop Protocol (RDP) is one of the most targeted Windows services.

SOC analysts should investigate:

* Repeated failed logons
* Successful logons following brute-force attempts
* Suspicious source IP addresses
* Unexpected remote sessions

Poorly secured RDP is a frequent entry point for ransomware attacks.

---

## Phishing

Phishing remains one of the most successful Initial Access techniques.

Attackers commonly deliver:

* Executable malware
* Compressed archives (.zip / .rar)
* LNK shortcut files
* PowerShell-based payloads

Windows hiding known file extensions makes double-extension files (e.g. `invoice.pdf.exe`) especially dangerous.

---

## LNK Attachments

LNK files are Windows shortcut files that attackers abuse to execute malicious commands while appearing harmless.

Unlike executable files, LNK shortcuts often leave fewer execution artifacts.

A useful hunting technique is correlating:

* File creation events in the **Downloads** folder
* Subsequent PowerShell execution
* Process creation logs
* Parent-child process relationships

---

## Detecting Malicious Downloads

Sysmon provides valuable telemetry for detecting phishing payloads.

Analysts should monitor:

* Downloads of executable files
* Newly created files in Downloads
* Execution of suspicious binaries
* PowerShell launched shortly after downloaded files

Correlating file creation and process creation events helps reconstruct the attack chain.

---

## Removable Media

USB devices remain an effective Initial Access technique.

Common attacker methods include:

* Malicious LNK shortcuts
* Fake folders
* Double-extension executable files
* Malware disguised as legitimate documents

These attacks can bypass perimeter security because they originate from local media instead of the Internet.

---

# Key Takeaways

* Initial Access usually begins through exposed services or user interaction.
* RDP remains one of the highest-risk Windows services.
* Phishing continues to be one of the most successful attack techniques.
* LNK shortcuts are commonly used to execute PowerShell payloads.
* Sysmon significantly improves visibility into phishing and malware execution.
* Correlating multiple Windows events is essential for accurate investigations.

---

# SOC Perspective

SOC analysts should focus on detecting the earliest signs of compromise. Monitoring authentication logs, Sysmon process creation, PowerShell activity, file creation events, and suspicious parent-child process relationships allows analysts to identify phishing attacks, exposed-service compromises, and malicious USB activity before attackers establish persistence or move laterally.
