# Room: IP and Domain Threat Intelligence

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Understand how SOC analysts investigate suspicious IP addresses and domain names by enriching alerts with DNS information, WHOIS/RDAP data, IP reputation, Autonomous System information, exposed services, TLS certificates, VPN detection, and external threat intelligence.

The room focuses on transforming raw network indicators into actionable intelligence during security investigations.

---

# Topics Covered

- Domain Intelligence
- DNS Records
- WHOIS & RDAP
- Domain Reputation
- Typosquatting
- IDN (Homograph) Attacks
- CDN Infrastructure
- IP Reputation
- VirusTotal
- AbuseIPDB
- Autonomous Systems (ASN)
- Shodan
- Censys
- TLS Certificates
- VPN Detection
- SOC Investigation Workflow

---

# Key Concepts

## IP and Domain Threat Intelligence

IP addresses and domain names are among the most common Indicators of Compromise (IOCs) encountered during SOC investigations.

However, an IP or domain alone provides very little context.

Analysts must determine questions such as:

- Is the domain legitimate?
- Is the IP malicious?
- Does the infrastructure belong to a cloud provider?
- Is the domain newly registered?
- Has the indicator appeared in previous attacks?
- Is the connection expected for this user or device?

Threat intelligence enrichment helps answer these questions before analysts decide whether an alert represents malicious activity.

---

# Domain Investigation

Domain analysis is often the first step when investigating phishing, malware communication, or suspicious web traffic.

Useful questions include:

- Does the domain look legitimate?
- Is it newly registered?
- Does it imitate another company?
- Does it resolve to suspicious infrastructure?
- Does it appear in threat intelligence feeds?
- Is it associated with known malware?

---

# DNS Resolution

DNS records reveal how a domain is configured and where traffic is directed.

---

## A Records

An **A record** maps a domain name to an IPv4 address.

Example:

```text
example.com → 192.0.2.15
```

The resolved IP can then be investigated using reputation services and infrastructure analysis.

---

## AAAA Records

AAAA records perform the same function for IPv6 addresses.

Example:

```text
example.com → 2001:db8::15
```

IPv6 infrastructure should be investigated in the same way as IPv4.

---

## TXT Records

TXT records often provide useful security information.

Examples include:

- SPF records
- DKIM configuration
- DMARC policies
- Email providers
- Verification tokens
- Cloud services

Legitimate organizations commonly have well-maintained TXT records.

Suspicious observations may include:

- Missing email security records
- Fake or malformed SPF entries
- Unexpected third-party services
- Recently created infrastructure

Although these indicators do not prove maliciousness, they provide valuable investigative context.

---

# WHOIS and RDAP

WHOIS has historically provided registration information for domains.

Modern deployments increasingly use **RDAP (Registration Data Access Protocol)**, which provides structured registration information through standardized APIs.

Useful information includes:

- Registrar
- Registration date
- Expiration date
- Name servers
- Registration status
- Domain age

---

## Domain Age

One of the most useful enrichment fields is domain age.

Generally:

- Long-established domains are less suspicious.
- Newly registered domains deserve additional investigation.

Many phishing domains exist for only:

- Days
- Weeks
- A few months

because attackers frequently abandon or rotate infrastructure.

Domain age alone does not prove legitimacy, but it is an important risk factor.

---

# Domain Reputation

Threat intelligence platforms may provide information such as:

- Malware associations
- Phishing reports
- Community reputation
- Threat categories
- Passive DNS history
- Previous observations

A domain with multiple independent reports of malicious activity deserves higher investigative priority.

---

# Common DNS-Based Attack Techniques

## CDN Abuse

Many attackers hide malicious infrastructure behind legitimate Content Delivery Networks.

Examples include:

- Cloudflare
- Amazon CloudFront
- Fastly
- Akamai

When a domain resolves to a CDN address, the IP itself often provides little value because thousands of unrelated websites may share the same infrastructure.

Analysts should continue investigating the domain rather than assuming the CDN IP is malicious.

---

## Typosquatting

Typosquatting attempts to deceive users by registering domains that closely resemble legitimate brands.

Examples:

```text
micros0ft.com
```

```text
gooogle.com
```

```text
tryhakme.com
```

These domains rely on users overlooking small spelling differences.

Some organizations intentionally register common misspellings defensively, so analysts should verify ownership before assuming malicious intent.

---

## IDN (Homograph) Attacks

Internationalized Domain Names allow Unicode characters.

Attackers abuse this by replacing Latin letters with visually identical characters from other alphabets.

Example:

```text
tryhаckme.com
```

The "a" may actually be a Cyrillic character.

Although the domain appears identical, it resolves differently.

When suspicious Unicode characters are present, analysts can convert the domain into its **Punycode** representation.

Example:

```text
xn--
```

Domains beginning with **xn--** should receive additional scrutiny.

---

# IP Address Enrichment

Most SIEM and EDR alerts include at least one IP address.

However, an IP may belong to:

- A home router
- A cloud provider
- A CDN
- A VPN service
- Shared hosting
- A compromised server
- A legitimate SaaS provider

Without enrichment, analysts risk:

- Blocking legitimate infrastructure
- Missing malicious infrastructure
- Misclassifying alerts

---

# VirusTotal for IP Investigation

VirusTotal provides reputation information for IP addresses.

Useful information includes:

- Vendor detections
- Community comments
- Related domains
- Associated files
- Historical observations

Unlike file hashes, even a small number of reputable detections for a non-CDN IP deserves investigation.

---

# AbuseIPDB

AbuseIPDB focuses on abuse reports submitted by security researchers and organizations.

It commonly reports:

- Port scanning
- Brute-force attacks
- SSH abuse
- Malware activity
- Botnet participation

Analysts should review:

- Abuse confidence score
- Number of reports
- Report categories
- Last reported activity

Community reports should support—not replace—internal evidence.

---

# Autonomous Systems (ASN)

An Autonomous System (AS) is a group of IP prefixes managed by a single organization.

Each AS is assigned a unique **ASN (Autonomous System Number)**.

Understanding the ASN helps analysts identify the likely role of an IP address.

---

## Residential Networks

Examples:

- Vodafone
- Comcast
- Orange

Alerts involving residential networks may indicate:

- Remote employees
- VPN usage
- Compromised home devices

Additional investigation is required.

---

## Hosting Providers

Examples:

- OVH
- DigitalOcean
- Hetzner
- PLAY2GO

Hosting providers are frequently abused for:

- Malware hosting
- Command-and-control servers
- Phishing sites
- VPS infrastructure

Hosting providers generally deserve more attention than residential ISPs.

---

## Cloud Providers

Examples:

- Amazon AWS
- Microsoft Azure
- Google Cloud

Cloud infrastructure is heavily used by legitimate organizations.

Unfortunately, attackers also deploy malware and phishing infrastructure inside cloud environments.

Cloud hosting should therefore be investigated using additional indicators rather than reputation alone.

---

# Geolocation

Geolocation can provide useful investigative context.

Examples include:

- Country
- City
- ISP
- Region

However, location should never be treated as definitive because attackers frequently use:

- VPNs
- Cloud servers
- Residential proxies
- Compromised systems

Location is one factor—not proof.

---

# Exposed Services

Knowing which services are exposed on an IP can provide valuable intelligence.

Examples:

- HTTP
- HTTPS
- SSH
- RDP
- FTP
- SMTP
- SMB

When investigating a victim system, exposed services may reveal the likely entry point.

When investigating an attacker IP, exposed services may indicate:

- Compromised servers
- Jump hosts
- Poorly secured infrastructure

---

# Shodan

Shodan continuously scans internet-connected devices.

It can reveal:

- Open ports
- Running services
- Operating systems
- Web technologies
- Banners
- Historical observations

SOC analysts frequently use Shodan to understand exposed infrastructure during investigations.

---

# Censys

Censys provides similar functionality to Shodan but focuses heavily on internet-wide scanning and certificate analysis.

It can identify:

- Services
- Open ports
- TLS certificates
- HTTP banners
- Software versions

Censys is particularly useful when investigating HTTPS infrastructure.

---

# TLS Certificate Analysis

HTTPS services expose TLS certificates that may reveal useful intelligence.

Important certificate fields include:

---

## Issuer

Self-signed certificates often deserve additional investigation.

Although not automatically malicious, many attacker-controlled servers use self-generated certificates.

---

## Validity

Recently issued certificates may indicate:

- Newly deployed phishing sites
- Fresh malware infrastructure
- Recently established command-and-control servers

Very long validity periods may also warrant investigation depending on context.

---

## Subject

Certificate subject fields sometimes reveal:

- Internal hostnames
- Software names
- Device types
- Firewall appliances
- Administrative interfaces

These details can help identify the underlying service.

---

# VPN Detection

Attackers frequently hide their true location through VPN providers.

Example scenario:

A Microsoft 365 login appears to originate from the user's city.

Without enrichment, the alert may appear legitimate.

However, VPN detection services may reveal that the IP belongs to:

- ProtonVPN
- NordVPN
- Mullvad
- Tor
- Commercial proxy providers

The apparent location therefore becomes unreliable.

---

# VPN Intelligence

Useful services include:

- IP2Proxy
- Spur

These platforms identify:

- VPN exit nodes
- Residential proxies
- Hosting providers
- Tor exit nodes
- Anonymous proxy services

Organizations often integrate this intelligence directly into SIEM or SOAR platforms.

---

# Investigation Workflow

A SOC analyst investigating a suspicious domain may follow this process:

1. Examine the domain name.
2. Look for typosquatting or homograph attacks.
3. Check domain reputation.
4. Review WHOIS/RDAP information.
5. Determine domain age.
6. Resolve DNS records.
7. Investigate the resulting IP address.
8. Identify CDN usage.
9. Review ASN ownership.
10. Check VirusTotal.
11. Check AbuseIPDB.
12. Determine VPN or proxy usage.
13. Review exposed services.
14. Examine TLS certificates.
15. Correlate findings with internal telemetry.

Not every step is required for every investigation, but this workflow provides a structured approach.

---

# Threat Intelligence Correlation

No single indicator should determine whether an alert is malicious.

Instead, analysts correlate multiple sources.

Example:

```text
Domain
↓
WHOIS
↓
DNS Records
↓
Resolved IP
↓
VirusTotal
↓
AbuseIPDB
↓
ASN
↓
VPN Detection
↓
Shodan/Censys
↓
TLS Certificate
↓
SIEM Evidence
```

The strongest conclusions come from multiple independent sources pointing toward the same assessment.

---

# Common Investigation Mistakes

SOC analysts should avoid:

- Blocking an entire CDN IP range.
- Trusting geolocation without checking VPN usage.
- Assuming every newly registered domain is malicious.
- Ignoring legitimate defensive typosquatting domains.
- Relying solely on VirusTotal detections.
- Assuming cloud infrastructure is malicious simply because it hosts attacker activity.
- Treating IP reputation as proof of compromise.

Every indicator must be interpreted within the context of the alert.

---

# Key Takeaways

- Domains and IP addresses require enrichment before making security decisions.
- DNS records reveal how domains are configured.
- WHOIS and RDAP provide valuable registration information.
- Domain age is an important contextual indicator.
- Typosquatting and IDN attacks are common phishing techniques.
- CDN infrastructure can obscure attacker origin servers.
- VirusTotal and AbuseIPDB provide complementary IP reputation data.
- ASN analysis helps identify the type of infrastructure behind an IP.
- Shodan and Censys reveal exposed internet-facing services.
- TLS certificates can provide additional infrastructure intelligence.
- VPN detection helps distinguish genuine users from anonymized connections.
- Multiple intelligence sources should be correlated before reaching a conclusion.

---

# SOC Perspective

Network indicators are among the most common artifacts encountered during SOC investigations, but they rarely tell the full story on their own. A suspicious IP address may belong to a cloud provider, a CDN, a VPN service, or a compromised server, while a newly registered domain may be either part of a phishing campaign or a legitimate business launch.

A SOC analyst should enrich every IP address and domain using multiple threat intelligence sources, then correlate that information with internal evidence such as authentication logs, DNS queries, process execution, endpoint telemetry, and network connections. Effective investigations are based on context and corroborating evidence—not on a single reputation score or indicator.
