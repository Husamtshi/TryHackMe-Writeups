# Room: Sysmon

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how Sysmon extends Windows Event Logging by providing detailed system telemetry, understand its configuration, analyze important Event IDs, and use Sysmon for threat hunting and incident investigations.

---

# Topics Covered

* Sysmon Overview
* Sysmon Configuration
* Sysmon Best Practices
* Event IDs
* PowerShell Filtering
* Threat Hunting
* Malware Detection
* Persistence Detection
* Evasion Techniques

---

# Key Concepts

## What is Sysmon?

Sysmon (System Monitor) is a Windows system service that provides detailed logging of system activity.

Unlike standard Windows Event Logs, Sysmon records additional information that helps security analysts detect malicious behavior and investigate incidents.

Sysmon events are stored in:

`Applications and Services Logs → Microsoft → Windows → Sysmon → Operational`

---

## Sysmon Configuration

Sysmon requires a configuration file to define which events should be collected.

Well-designed configurations reduce unnecessary logs while improving visibility into suspicious activity.

A commonly used community configuration is maintained by **SwiftOnSecurity**.

---

## Best Practices

Important recommendations include:

* Prefer **Exclude** rules over **Include** rules to reduce the risk of missing important events.
* Use command-line tools such as **Get-WinEvent** and **wevtutil** for advanced filtering.
* Understand your environment before writing detection rules so you can distinguish normal activity from suspicious behavior.

---

## Important Event IDs

Some of the most useful Sysmon Event IDs include:

* Event ID 1 – Process Creation
* Event ID 3 – Network Connection
* Event ID 7 – Image Loaded
* Event ID 8 – CreateRemoteThread
* Event ID 11 – File Created
* Event IDs 12, 13, 14 – Registry Events
* Event ID 15 – FileCreateStreamHash
* Event ID 22 – DNS Query

Understanding these Event IDs is essential during investigations.

---

## PowerShell Filtering

Sysmon logs can be filtered using:

* Get-WinEvent
* XPath Queries
* wevtutil

Filtering by Event ID, XML attributes, or event data allows analysts to quickly locate relevant events.

---

## Threat Hunting

Sysmon is widely used for hypothesis-based threat hunting.

Examples include hunting for:

* Metasploit activity
* Suspicious processes
* Command and Control (C2) communication
* Malware execution
* Unusual network connections

---

## Detecting Mimikatz

Sysmon can help identify Mimikatz activity by monitoring:

* LSASS-related activity
* Credential dumping behavior
* Remote thread creation
* Suspicious process execution
* Malicious file creation

---

## Malware Detection

Sysmon provides visibility into malware behavior such as:

* Remote Access Trojans (RATs)
* Backdoors
* Suspicious process execution
* Network communication
* Malware persistence

---

## Persistence Detection

Common persistence techniques include:

* Registry modifications
* Startup scripts
* Scheduled execution

Sysmon can detect these activities through Registry and File Creation events.

---

## Evasion Techniques

Attackers may attempt to avoid detection using techniques such as:

* Alternate Data Streams (ADS)
* DLL Injection
* Thread Injection
* Obfuscation
* Masquerading
* Packing and Compression

Sysmon records events that help analysts detect many of these techniques.

---

# Key Takeaways

* Sysmon extends Windows Event Logs with detailed security telemetry.
* A well-designed configuration is critical for effective monitoring.
* Event IDs provide valuable insight into system activity.
* PowerShell and XPath improve log filtering capabilities.
* Sysmon is an essential tool for threat hunting and malware investigations.
* Understanding attacker techniques improves detection quality.

---

# SOC Perspective

Sysmon is one of the most valuable telemetry sources for SOC analysts. It provides detailed visibility into process creation, network activity, registry changes, DNS queries, and persistence mechanisms. Combined with a SIEM such as Splunk, Sysmon enables analysts to detect malicious behavior, investigate incidents, and build high-quality detection rules.
