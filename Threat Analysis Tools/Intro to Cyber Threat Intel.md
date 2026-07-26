# Room: Intro to Cyber Threat Intelligence

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Understand the fundamentals of Cyber Threat Intelligence, including intelligence classifications, information-sharing controls, vulnerability intelligence, and the standards used to structure and exchange threat data.

The room also explains how SOC analysts use threat intelligence to enrich alerts, prioritize investigations, identify adversary behavior, and make better security decisions.

---

# Topics Covered

- Cyber Threat Intelligence
- Threat Intelligence Classifications
- Strategic Intelligence
- Tactical Intelligence
- Operational Intelligence
- Technical Intelligence
- Threat Intelligence Lifecycle
- Traffic Light Protocol
- CVE, CVSS, and NVD
- Indicators of Compromise
- Tactics, Techniques, and Procedures
- STIX
- TAXII
- Threat Intelligence Sharing
- Intelligence Enrichment

---

# Key Concepts

## Cyber Threat Intelligence

Cyber Threat Intelligence, commonly abbreviated as **CTI**, is analyzed information about threats that helps an organization make security decisions.

Threat intelligence attempts to answer questions such as:

- Who may target the organization?
- Why would they target it?
- Which systems or information are at risk?
- What techniques are likely to be used?
- Which indicators are associated with the activity?
- How should defenders detect and respond to the threat?

Threat intelligence is more than a collection of IP addresses or file hashes.

For information to become useful intelligence, it must be:

- Relevant
- Accurate
- Timely
- Contextual
- Actionable

---

# Data, Information, and Intelligence

These terms are related but not identical.

## Data

Raw observations without significant context.

Examples:

```text
185.20.10.5
```

```text
e3b0c44298fc1c149afbf4c8996fb924...
```

## Information

Data that has been organized or given context.

Example:

```text
The IP address 185.20.10.5 communicated with three internal systems.
```

## Intelligence

Information that has been analyzed and can support a decision.

Example:

```text
The IP address is associated with an active credential-stealing campaign
targeting healthcare organizations. The affected endpoints should be isolated
and searched for related persistence activity.
```

The value of threat intelligence comes from the context and action it enables.

---

# Threat Intelligence Classifications

Threat intelligence can be divided into four main classifications:

- Strategic
- Tactical
- Operational
- Technical

Each type serves a different audience and decision-making purpose.

---

## Strategic Threat Intelligence

Strategic intelligence provides a high-level view of the threat landscape.

It is commonly used by:

- Executives
- Security leadership
- Risk-management teams
- Business decision-makers

Strategic intelligence may include:

- Industry threat trends
- Geopolitical developments
- Emerging risks
- Threats to business operations
- Long-term attacker patterns
- Changes in cybercrime activity

Example:

```text
An annual report predicts an increase in data theft and destructive attacks
against healthcare organizations.
```

Strategic intelligence generally supports long-term planning rather than immediate incident investigation.

---

## Tactical Threat Intelligence

Tactical intelligence describes how threat actors perform attacks.

It focuses on adversary:

- Tactics
- Techniques
- Procedures

These are commonly referred to as **TTPs**.

Examples include:

- PowerShell execution
- Credential dumping
- Scheduled-task persistence
- Phishing attachments
- WMI lateral movement
- DLL side-loading

Tactical intelligence is commonly mapped to frameworks such as MITRE ATT&CK.

Example:

```text
A threat advisory describes attackers using malicious Visual Basic scripts
for execution through T1059.005.
```

Tactical intelligence helps:

- SOC analysts
- Detection engineers
- Threat hunters
- Incident responders

It is generally more durable than technical indicators because attacker techniques often remain useful even after infrastructure changes.

---

## Operational Threat Intelligence

Operational intelligence focuses on a specific attack, campaign, or threat actor operation.

It may describe:

- Attacker objectives
- Intended victims
- Campaign timing
- Targeted industries
- Delivery methods
- Infrastructure
- Malware families
- Likely next steps

Example:

```text
A ransomware group is preparing a phishing campaign against European hospitals
using fake medical invoices.
```

Operational intelligence helps organizations determine:

- Whether they are likely to be targeted
- Which assets are at risk
- Which attack paths should be monitored
- Which defensive controls should be prioritized

This type of intelligence is often sensitive and may have a limited useful lifetime.

---

## Technical Threat Intelligence

Technical intelligence contains specific indicators and artifacts associated with malicious activity.

Examples include:

- IP addresses
- Domain names
- URLs
- File hashes
- Email addresses
- Registry keys
- File names
- Mutex values
- Digital certificates
- User-Agent strings

These artifacts are commonly called **Indicators of Compromise**, or **IOCs**.

Technical intelligence can be used to:

- Search SIEM logs
- Block malicious domains
- Update firewall rules
- Detect known malware
- Enrich alerts
- Hunt across endpoints

Technical indicators are often highly actionable but may become outdated quickly.

Attackers can easily change:

- IP addresses
- Domains
- File hashes
- File names

Therefore, technical intelligence should be combined with behavioral and tactical intelligence.

---

# Intelligence Durability

Different types of intelligence have different lifetimes.

A useful way to understand this is through the **Pyramid of Pain**.

From easiest to hardest for attackers to change:

1. File hashes
2. IP addresses
3. Domain names
4. Network and host artifacts
5. Tools
6. Tactics, techniques, and procedures

File hashes are easy for attackers to change by modifying the malware.

TTP-based detections are more difficult to evade because the attacker may need to change how the entire operation works.

---

# Threat Intelligence Lifecycle

Threat intelligence is produced through a continuous lifecycle.

## 1. Direction

Define the intelligence requirements.

Questions may include:

- Which threat actors target our industry?
- Are any campaigns targeting our organization?
- Which ransomware groups are currently active?
- What intelligence would improve our detections?

---

## 2. Collection

Gather relevant data from sources such as:

- Internal security logs
- Malware analysis
- Incident reports
- Threat feeds
- Vendor reports
- Government advisories
- Information-sharing communities
- Open-source intelligence

---

## 3. Processing

Convert collected data into a usable format.

This may involve:

- Normalizing indicators
- Removing duplicates
- Parsing reports
- Translating content
- Extracting IOCs
- Converting data into STIX objects
- Assigning timestamps and confidence levels

---

## 4. Analysis

Evaluate the information and determine its meaning.

Analysts may:

- Correlate multiple sources
- Assess reliability
- Identify patterns
- Determine relevance
- Map behavior to MITRE ATT&CK
- Estimate attacker intent
- Produce recommendations

This stage transforms information into intelligence.

---

## 5. Dissemination

Share intelligence with the appropriate audience.

Possible outputs include:

- SOC alerts
- Threat advisories
- Executive reports
- Detection rules
- IOC feeds
- Intelligence bulletins
- Incident-response guidance

The level of detail should match the audience.

---

## 6. Feedback

Determine whether the intelligence met its objective.

Feedback can identify:

- Missing information
- Incorrect assumptions
- New intelligence requirements
- Improvements to collection
- Necessary detection updates

The lifecycle then begins again.

---

# Traffic Light Protocol

The **Traffic Light Protocol**, or **TLP**, defines how sensitive information may be shared.

TLP labels help prevent intelligence from being distributed beyond its intended audience.

The current TLP framework includes:

- TLP:CLEAR
- TLP:GREEN
- TLP:AMBER
- TLP:AMBER+STRICT
- TLP:RED

---

## TLP:CLEAR

Information may be shared without restriction.

Examples of appropriate use:

- Public reports
- Public advisories
- Open-source threat intelligence
- Public security documentation

SOC analysts may generally place this information in:

- Public reports
- Internal wikis
- Shared knowledge bases
- Public detection repositories

---

## TLP:GREEN

Information may be shared within a trusted community but should not be made publicly available.

Examples include sharing with:

- Industry peers
- Partner organizations
- Security communities
- Professional working groups

SOC analysts should not publish TLP:GREEN information on public websites or social media.

---

## TLP:AMBER

Information may be shared on a limited need-to-know basis within the recipient's organization and, where necessary, with clients or customers who need the information to protect themselves.

It should not be broadly distributed.

SOC analysts should:

- Keep it inside approved systems
- Limit access
- Avoid copying it into public tickets
- Confirm sharing permissions before redistribution

---

## TLP:AMBER+STRICT

Information is limited to the recipient's organization.

It should not be shared with clients, partners, or external parties unless separately authorized.

This label is more restrictive than standard TLP:AMBER.

---

## TLP:RED

Information is restricted to specifically named recipients.

It should not be shared outside the meeting, conversation, or designated group in which it was provided.

SOC analysts should:

- Avoid placing it in broadly accessible tickets
- Store it only in approved protected systems
- Not forward it without explicit permission
- Confirm authorization before using it outside the original context

---

# TLP Summary

| TLP Label | Sharing Boundary |
|---|---|
| TLP:CLEAR | May be shared publicly |
| TLP:GREEN | May be shared within trusted communities |
| TLP:AMBER | Limited need-to-know sharing |
| TLP:AMBER+STRICT | Restricted to the recipient organization |
| TLP:RED | Restricted to named recipients |

TLP controls how information may be shared. It does not indicate whether the intelligence is accurate or how severe the threat is.

---

# Source Reliability and Information Confidence

Threat intelligence should not automatically be trusted simply because it appears in a feed or report.

Analysts should consider:

- Who produced the intelligence?
- Has the source been reliable before?
- Is the information confirmed by other sources?
- How recent is it?
- Is it relevant to the organization?
- Is it based on observation or speculation?
- What confidence level was assigned?

A threat feed may contain:

- False positives
- Expired infrastructure
- Shared hosting addresses
- Legitimate services abused by attackers
- Incorrect malware classifications

Threat intelligence should enrich an investigation, not replace analytical judgment.

---

# Vulnerability Intelligence

Threat intelligence also includes information about software vulnerabilities.

SOC analysts may receive alerts and advisories involving:

- Newly disclosed vulnerabilities
- Exploited vulnerabilities
- Vulnerable products
- Available patches
- Proof-of-concept exploits
- Active threat campaigns

Three important vulnerability concepts are:

- CVE
- CVSS
- NVD

---

# CVE

**CVE** stands for Common Vulnerabilities and Exposures.

A CVE identifier provides a standardized reference for a publicly disclosed vulnerability.

Example:

```text
CVE-2023-4863
```

A CVE identifier normally follows this format:

```text
CVE-YEAR-NUMBER
```

The CVE entry may include:

- A short description
- References
- Affected products
- Publication information
- The organization that assigned the identifier

A CVE number identifies a vulnerability; it does not by itself show whether the vulnerability is actively exploited or relevant to a specific environment.

---

# CVSS

**CVSS** stands for Common Vulnerability Scoring System.

It provides a standardized method for describing vulnerability severity.

CVSS scores range from:

```text
0.0 to 10.0
```

Common severity ranges are:

| Score | Severity |
|---|---|
| 0.0 | None |
| 0.1–3.9 | Low |
| 4.0–6.9 | Medium |
| 7.0–8.9 | High |
| 9.0–10.0 | Critical |

CVSS considers technical factors such as:

- Attack vector
- Attack complexity
- Required privileges
- User interaction
- Impact on confidentiality
- Impact on integrity
- Impact on availability

A high CVSS score does not automatically mean a vulnerability is the organization's highest priority.

Prioritization should also consider:

- Whether the product exists in the environment
- Whether the system is internet-facing
- Whether exploitation is active
- Availability of public exploit code
- Asset criticality
- Compensating controls
- Business impact

---

# NVD

The **National Vulnerability Database**, or **NVD**, provides enriched vulnerability information.

It commonly includes:

- CVE descriptions
- CVSS scores
- Affected product mappings
- References
- Weakness classifications
- Searchable vulnerability metadata

The CVE Program assigns and maintains CVE identifiers through authorized organizations.

The NVD consumes CVE information and enriches it with additional analysis and metadata.

---

# Additional Vulnerability Intelligence Sources

## CISA Known Exploited Vulnerabilities

A vulnerability appearing in a known-exploited catalogue may deserve greater priority because it has evidence of real-world exploitation.

This can be more operationally useful than looking only at the CVSS score.

## Vendor Advisories

Vendors often provide:

- Affected versions
- Patch instructions
- Mitigations
- Exploitation information
- Product-specific context

## Exploit Availability

The existence of a public proof-of-concept or working exploit can increase risk, especially for exposed systems.

---

# Vulnerability Triage Workflow

When receiving a vulnerability notification, a SOC analyst may ask:

1. Is the affected product present in the environment?
2. Which versions are installed?
3. Are any affected systems internet-facing?
4. Is the vulnerability actively exploited?
5. Is exploit code publicly available?
6. What is the CVSS score and vector?
7. Are critical assets affected?
8. Is a patch or mitigation available?
9. Is there evidence of attempted exploitation?
10. Should the issue be escalated?

This helps prevent teams from prioritizing vulnerabilities solely by score.

---

# STIX

**STIX** stands for Structured Threat Information Expression.

STIX is a standardized language and data model for representing cyber threat intelligence.

It is commonly represented in JSON.

STIX can describe objects such as:

- Indicators
- Malware
- Threat actors
- Attack patterns
- Vulnerabilities
- Campaigns
- Infrastructure
- Identity
- Relationships
- Observed data

Example relationships might include:

```text
Threat Actor → Uses → Malware
```

```text
Malware → Communicates With → Domain
```

```text
Campaign → Targets → Organization
```

```text
Indicator → Indicates → Malware
```

STIX allows different security tools and organizations to describe threat information consistently.

---

# TAXII

**TAXII** stands for Trusted Automated eXchange of Intelligence Information.

TAXII is an application-layer protocol and API used to exchange cyber threat intelligence.

STIX and TAXII serve different purposes:

- **STIX** defines how threat information is structured.
- **TAXII** defines how that information is transported and exchanged.

A simplified comparison is:

```text
STIX = The threat intelligence content
TAXII = The delivery mechanism
```

---

## TAXII Collections

Modern TAXII implementations commonly organize threat intelligence into **Collections**.

A collection may contain:

- Malware indicators
- Phishing infrastructure
- Ransomware intelligence
- Industry-specific threats
- Vulnerability information

Authorized clients can connect to the TAXII server and retrieve or submit intelligence depending on their permissions.

Older TAXII versions also used a channel-based publish-and-subscribe model, but TAXII 2.x primarily uses API Roots and Collections.

---

# Indicators of Compromise

Indicators of Compromise are observable artifacts associated with potentially malicious activity.

Examples include:

```text
File hash
IP address
Domain name
URL
Email sender
Registry key
File path
Mutex
Certificate
User-Agent
```

IOCs may be used for:

- Alert enrichment
- Historical searches
- Threat hunting
- Blocking
- Malware identification
- Incident scoping

However, an IOC match does not always prove compromise.

For example:

- An IP may belong to shared hosting
- A domain may have been cleaned
- A hash may belong to a legitimate tool
- A security researcher may have contacted malicious infrastructure

Context must always be reviewed.

---

# Indicators of Attack

Indicators of Attack focus on suspicious behavior rather than static artifacts.

Examples include:

- Office spawning PowerShell
- Credential dumping from LSASS
- Repeated failed logins followed by success
- A scheduled task launching from a temporary directory
- Remote process creation through WMI
- PowerShell downloading and executing encoded content

Behavioral indicators are often more durable than atomic IOCs.

---

# Threat Intelligence Platforms

Threat Intelligence Platforms help organizations:

- Store intelligence
- Normalize indicators
- Remove duplicates
- Add confidence scores
- Manage relationships
- Share intelligence
- Integrate with SIEM and security tools

A commonly used open-source platform is:

```text
MISP
```

MISP can store and share:

- Indicators
- Malware families
- Threat actors
- Campaigns
- Relationships
- TLP classifications
- Tags and confidence information

---

# Applying Threat Intelligence in a SOC

Threat intelligence can support several SOC activities.

## Alert Enrichment

An alert containing an external IP can be enriched with:

- Reputation
- Associated malware
- Threat actor information
- First-seen and last-seen dates
- Geolocation
- Related domains
- Confidence score

---

## Threat Hunting

Analysts can search historical logs for:

- Malicious hashes
- Domains
- IP addresses
- File names
- Commands
- Known attacker techniques

---

## Detection Engineering

Tactical intelligence can be converted into:

- SIEM detection rules
- EDR behavioral rules
- YARA rules
- Sigma rules
- Network signatures
- Email filtering rules

---

## Incident Response

Operational intelligence can help determine:

- The likely attacker objective
- Expected next steps
- Common persistence mechanisms
- Data typically targeted
- Whether additional malware may be deployed

---

## Vulnerability Prioritization

Threat intelligence helps determine whether a vulnerability is:

- Actively exploited
- Used by ransomware groups
- Relevant to the organization
- Associated with public exploit code
- Targeting an exposed service

---

# Example Investigation

Suppose a SIEM alert detects communication with a suspicious IP address.

A SOC analyst should not immediately declare the host compromised.

The analyst should determine:

1. What process created the connection?
2. Which user was active?
3. What threat intelligence source reported the IP?
4. How reliable is the source?
5. When was the IP last observed as malicious?
6. Is it shared infrastructure?
7. Did other hosts connect to it?
8. Were any files downloaded?
9. Did suspicious child processes execute?
10. Does the behavior match a known campaign?

Threat intelligence provides context, while endpoint and network evidence determine what actually occurred.

---

# Key Takeaways

- Threat intelligence connects adversary information to organizational risk.
- Strategic intelligence supports long-term business decisions.
- Tactical intelligence describes attacker TTPs.
- Operational intelligence focuses on active campaigns and attacker intent.
- Technical intelligence contains IOCs and artifacts.
- Technical indicators are actionable but may expire quickly.
- TTP-based intelligence is generally more durable.
- TLP defines how intelligence may be shared.
- TLP does not represent confidence or threat severity.
- CVE provides a standardized vulnerability identifier.
- CVSS describes technical severity.
- NVD enriches vulnerability records with additional metadata.
- Vulnerabilities should not be prioritized by CVSS alone.
- STIX structures threat-intelligence data.
- TAXII transports and exchanges threat-intelligence data.
- Intelligence sources must be evaluated for reliability and relevance.
- Threat intelligence should support investigation, not replace evidence.

---

# SOC Perspective

Cyber Threat Intelligence helps SOC analysts transform isolated alerts into meaningful investigations.

A hash, IP address, or domain has limited value without context. Threat intelligence can reveal whether an indicator is associated with a known malware family, threat actor, campaign, or attack technique. However, analysts must validate that information against internal telemetry before reaching a conclusion.

For a SOC Level 1 analyst, the most important skills are understanding the type of intelligence being used, respecting its TLP sharing restrictions, evaluating source reliability, enriching alerts, and recognizing when a threat or vulnerability should be escalated.
