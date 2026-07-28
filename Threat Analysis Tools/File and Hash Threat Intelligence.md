# Room: File and Hash Threat Intelligence

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Understand how file artifacts and cryptographic hashes are used in Cyber Threat Intelligence to identify, classify, validate, and investigate malicious files.

This room also introduces file path analysis, filename heuristics, hash generation, threat intelligence platforms, malware repositories, and dynamic analysis techniques used by SOC analysts.

---

# Topics Covered

- File Path Analysis
- Filename Heuristics
- Cryptographic Hashes
- SHA-256
- VirusTotal
- MalwareBazaar
- Malware Metadata
- Dynamic Malware Analysis
- Sandboxing
- Runtime Indicators
- Malware Evasion
- Threat Intelligence Correlation

---

# Key Concepts

## File-Based Threat Intelligence

Files are one of the most common artifacts encountered during security investigations.

When investigating a suspicious file, analysts aim to answer questions such as:

- Is the file malicious?
- Has it been seen before?
- Which malware family is it associated with?
- Which systems executed it?
- What behavior does it perform?
- What indicators can be extracted?
- Which threat actor or campaign is associated with it?

Rather than relying on a single indicator, SOC analysts combine file metadata, hashes, filenames, runtime behavior, and external threat intelligence to reach a conclusion.

---

# File Path Analysis

A file's storage location often provides valuable context during an investigation.

Attackers frequently choose locations that reduce suspicion while allowing their malware to execute successfully.

Suspicious locations include:

```text
C:\
```

The system drive may contain persistence mechanisms or manually placed payloads.

---

```text
C:\Users\Public\
```

The Public profile is accessible by multiple users and is frequently abused to store malware or shared tools.

---

```text
C:\Users\Public\Downloads\
```

A high-traffic directory that may receive less scrutiny during routine monitoring.

---

```text
C:\Windows\Temp\
```

Often used for temporary payloads, installers, unpacked malware, or scripts that are deleted shortly after execution.

---

```text
C:\ProgramData\
```

Commonly abused because it is writable and frequently used by legitimate applications, making malicious files blend into normal activity.

---

## Why File Paths Matter

File paths provide immediate investigative context.

For example:

- A Microsoft executable inside **System32** may be expected.
- The same executable inside **Temp** deserves investigation.
- A PowerShell script stored in **AppData** may indicate user-level persistence.
- Malware running from **Downloads** shortly after an email attachment is opened may suggest phishing.

File location alone does not prove malicious activity, but it significantly influences investigative priority.

---

# Filename Heuristics

Threat actors frequently manipulate filenames to avoid attracting attention.

Common techniques include:

---

## Double Extensions

Example:

```text
invoice.pdf.exe
```

Windows often hides known file extensions by default.

A victim may only see:

```text
invoice.pdf
```

while actually executing an executable.

---

## System Binary Impersonation

Attackers imitate legitimate Windows processes.

Example:

```text
scvhost.exe
```

instead of

```text
svchost.exe
```

Only a single character has changed.

Analysts should verify both:

- File name
- File location

A legitimate Windows binary running outside its expected directory should always be investigated.

---

## High-Entropy Filenames

Examples:

```text
jh8F21.exe
```

```text
7ADK31.tmp
```

Randomized names often indicate:

- Packed malware
- Automatically generated payloads
- Polymorphic malware
- High-volume phishing campaigns

Although random filenames are not always malicious, they deserve additional attention.

---

## Masquerading

Attackers often choose filenames that resemble normal business files.

Examples:

```text
backup-2024.exe
```

```text
Payroll_Update.exe
```

```text
WindowsUpdate.exe
```

```text
AdobeSync.exe
```

These names attempt to reduce suspicion by appearing legitimate.

---

# File Metadata

Every file contains metadata that can support an investigation.

Useful properties include:

- File size
- File type
- Compilation timestamp
- Digital signature
- Company name
- Version information
- Architecture
- PE sections
- Imports
- Entropy

Metadata alone does not prove maliciousness but helps build investigative context.

---

# Cryptographic Hashes

Hashes uniquely represent file contents.

They allow analysts to:

- Identify known malware
- Search threat intelligence databases
- Compare files
- Detect duplicates
- Track malware across investigations
- Share indicators efficiently

A hash changes completely if even one byte of the file changes.

---

## Common Hash Algorithms

### MD5

Fast but vulnerable to collisions.

Useful mainly for historical compatibility.

---

### SHA-1

Stronger than MD5 but no longer recommended for security-sensitive purposes.

---

### SHA-256

The industry standard for malware identification.

Advantages include:

- Strong collision resistance
- Widely supported
- Used by EDRs
- Used by VirusTotal
- Used by MalwareBazaar
- Common IOC format

SOC analysts should generally record SHA-256 whenever possible.

---

# Hash Generation

## Windows Command Prompt

```cmd
certutil -hashfile malware.exe SHA256
```

---

## PowerShell

```powershell
Get-FileHash -Algorithm SHA256 malware.exe
```

---

## Linux

```bash
sha256sum malware.exe
```

---

# Hash Investigation Best Practices

SOC analysts should:

- Store hashes in lowercase
- Record where the file was found
- Record when it was observed
- Hash both archives and extracted files
- Verify the original sample before analysis
- Include file path and host information

A hash without context provides very little investigative value.

---

# VirusTotal

VirusTotal is one of the most widely used malware intelligence platforms.

It aggregates results from dozens of security vendors into a single report.

VirusTotal is primarily used to determine whether a file has been previously observed and how other security vendors classify it.

---

## Important VirusTotal Information

### Detection Ratio

Example:

```text
63 / 72
```

This indicates how many vendors detected the file.

A higher detection ratio generally increases confidence but should never be treated as absolute proof.

---

### Threat Labels

Different vendors may classify the same sample differently.

Examples include:

- Trojan
- Downloader
- Stealer
- Ransomware
- Backdoor

Comparing multiple labels helps identify the malware family.

---

### Detection Rules

VirusTotal may expose:

- YARA rules
- Heuristic detections
- Behavioral detections
- Static signatures

These can help analysts understand why a file was detected.

---

### File Properties

Useful metadata includes:

- File type
- File size
- Compilation timestamp
- Digital signatures
- PE information

---

### Network Indicators

VirusTotal may provide:

- Domains
- IP addresses
- URLs
- DNS requests

These indicators can be searched within SIEM or EDR platforms.

---

### Dropped Files

Many reports include files created during execution.

These may become additional indicators of compromise.

---

# VirusTotal Limitations

VirusTotal is an intelligence source—not a verdict.

Analysts should remember:

- Low detections do not guarantee safety.
- High detections do not replace investigation.
- Newly released malware may have zero detections.
- False positives can occur.
- Vendors often disagree.

SOC investigations should always combine VirusTotal with internal telemetry.

---

# MalwareBazaar

MalwareBazaar is a malware repository used by researchers and defenders.

Unlike VirusTotal, MalwareBazaar focuses specifically on malware collection and intelligence.

---

## Malware Family Classification

Samples are grouped by malware family.

Examples include:

- IcedID
- Emotet
- Agent Tesla
- RedLine
- QakBot

A sample with only a few VirusTotal detections may still be confidently identified through MalwareBazaar family tagging.

---

## YARA Rules

Many samples include associated YARA rules.

Analysts may use these rules to:

- Hunt similar malware
- Improve detection coverage
- Identify related campaigns

---

## Campaign Attribution

Samples are often linked to known campaigns or threat actors.

Examples:

```text
TA551
APT29
FIN7
```

Campaign attribution helps correlate multiple incidents.

---

## Malware Samples

MalwareBazaar also provides downloadable malware samples for research in isolated environments.

These samples should only be handled inside properly secured malware analysis laboratories.

---

# Dynamic Malware Analysis

Static analysis reveals what a file is.

Dynamic analysis reveals what the file actually does.

This is performed inside an isolated sandbox.

---

## Sandboxes

A malware sandbox is an isolated virtual environment instrumented to monitor malware execution.

During execution, analysts observe:

- Process creation
- Registry modifications
- File activity
- Network connections
- Services
- Persistence
- DLL loading
- Child processes
- Memory activity

---

## Goals of Dynamic Analysis

SOC analysts commonly use sandboxes to:

### Confirm Execution

Determine whether the file actually performs malicious activity.

---

### Extract Runtime IOCs

Examples include:

- Domains
- IP addresses
- Mutexes
- Registry keys
- Scheduled tasks
- Services
- Dropped files

---

### Map Behavior

Modern sandboxes automatically map activity to:

- MITRE ATT&CK
- Malware families
- Detection rules

This accelerates reporting and incident response.

---

# Runtime Indicators

Runtime artifacts often provide stronger evidence than static metadata.

Examples include:

- PowerShell execution
- Process injection
- Registry persistence
- Network beaconing
- DLL loading
- Credential dumping
- Service creation
- Scheduled tasks

These behaviors help analysts determine attacker objectives.

---

# Sandboxing Limitations

Sandbox analysis is extremely valuable but not perfect.

---

## Sandbox Evasion

Modern malware frequently attempts to detect virtual environments.

Common techniques include:

- Virtual machine detection
- Hypervisor detection
- Anti-debugging
- Hardware fingerprinting
- Sandbox artifact detection

If a sandbox is detected, malware may avoid executing.

---

## Limited Execution Time

Many automated sandboxes execute samples for only a few minutes.

Delayed execution may never occur.

Examples include:

- Sleep timers
- Delayed payload downloads
- Multi-stage malware

---

## Encrypted Traffic

HTTPS communications may not always be fully inspected.

This can hide:

- Command-and-control traffic
- Downloaded payloads
- Exfiltrated data

DNS tunneling may also reduce visibility.

---

## Fileless Malware

Some attacks never create executable files on disk.

Examples include:

- PowerShell
- WMI
- Living Off the Land techniques
- In-memory payloads

Traditional file-based sandbox analysis may miss these attacks.

---

# Threat Intelligence Correlation

A mature investigation rarely relies on a single intelligence source.

SOC analysts should combine information from:

- VirusTotal
- MalwareBazaar
- Internal SIEM logs
- EDR telemetry
- Threat intelligence feeds
- Sandbox reports
- Malware analysis
- Process trees
- Network logs

The strongest conclusions come from multiple independent sources supporting the same finding.

---

# Investigation Workflow

When investigating a suspicious file, a SOC analyst should:

1. Validate the original file.
2. Record the file path.
3. Review the filename.
4. Generate a SHA-256 hash.
5. Search VirusTotal.
6. Search MalwareBazaar.
7. Review metadata.
8. Analyze runtime behavior in a sandbox.
9. Extract IOCs.
10. Search the SIEM for related activity.
11. Determine malware family or campaign.
12. Recommend containment actions.

---

# Key Takeaways

- File paths provide valuable investigative context.
- Attackers frequently abuse trusted directories.
- Filename heuristics can reveal masquerading techniques.
- Legitimate filenames should always be verified with their location.
- SHA-256 is the preferred hash for malware investigations.
- Hashes allow analysts to pivot into threat intelligence platforms.
- VirusTotal aggregates vendor detections and malware metadata.
- MalwareBazaar specializes in malware samples and family attribution.
- Dynamic analysis reveals runtime behavior that static analysis cannot.
- Sandboxes help extract behavioral indicators and ATT&CK mappings.
- Malware may evade sandbox analysis using anti-analysis techniques.
- Effective investigations combine multiple intelligence sources rather than relying on a single detection.

---

# SOC Perspective

For a SOC analyst, file investigation rarely ends after calculating a hash. A complete investigation combines file location, filename patterns, metadata, cryptographic hashes, external intelligence, runtime behavior, and internal telemetry to determine whether a file represents a real threat.

Threat intelligence platforms such as VirusTotal and MalwareBazaar provide valuable context, but they should support—not replace—evidence gathered from the organization's own environment. The strongest investigations correlate file artifacts with process execution, network activity, persistence mechanisms, and endpoint telemetry before making containment or escalation decisions.
