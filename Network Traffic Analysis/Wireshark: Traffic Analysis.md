# Room: Wireshark: Traffic Analysis

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how to investigate real-world network traffic using Wireshark by identifying reconnaissance, Man-in-the-Middle attacks, tunneling techniques, cleartext protocols, encrypted traffic, and credential exposure.

---

# Topics Covered

- Nmap Detection
- ARP Poisoning
- Man-in-the-Middle (MITM)
- DHCP
- NetBIOS
- Kerberos
- DNS Tunneling
- ICMP Tunneling
- FTP Analysis
- HTTP Analysis
- HTTPS Decryption
- Credential Hunting

---

# Key Concepts

## Detecting Nmap Scans

Reconnaissance is often the first stage of an attack.

Wireshark can help identify Nmap scans by observing:

- Multiple connection attempts to different ports.
- SYN scan patterns.
- Unusual TCP flag combinations.
- Rapid connections targeting many hosts.

Early detection of reconnaissance helps identify attackers before exploitation begins.

---

## ARP Poisoning & Man-in-the-Middle (MITM)

ARP Poisoning occurs when an attacker sends forged ARP replies to associate their MAC address with another device's IP address.

Common indicators include:

- Multiple ARP replies.
- Duplicate IP-to-MAC mappings.
- Frequent ARP broadcasts.
- Unexpected MAC address changes.

MITM attacks allow attackers to intercept, inspect, or modify network traffic between communicating hosts.

---

## Identifying Hosts

Several protocols help analysts identify hosts within a network.

Examples include:

- DHCP
- NetBIOS
- Kerberos

These protocols provide information such as:

- Hostnames
- IP addresses
- Domain membership
- Authentication activity

---

## DNS & ICMP Tunneling

Attackers may abuse legitimate protocols to bypass security controls.

### DNS Tunneling

Indicators include:

- Large numbers of DNS queries.
- Long or random-looking domain names.
- Repeated requests to the same domain.
- High DNS traffic volume.

### ICMP Tunneling

Indicators include:

- Large ICMP Echo packets.
- High ICMP traffic volume.
- Unexpected data inside ICMP payloads.

Both techniques are commonly used for:

- Command & Control (C2)
- Data Exfiltration

---

## FTP Analysis

FTP transmits data in plaintext.

Wireshark can reveal:

- Usernames
- Passwords
- Uploaded files
- Downloaded files
- FTP commands

FTP traffic should always be considered sensitive because credentials are transmitted without encryption.

---

## HTTP Analysis

HTTP traffic is also transmitted in plaintext.

Analysts can inspect:

- URLs
- HTTP Methods (GET / POST)
- Headers
- Cookies
- Parameters
- Downloaded files

HTTP analysis is valuable for investigating:

- Web attacks
- Malware downloads
- Web Shell activity
- Data exfiltration

---

## HTTPS Analysis

Unlike HTTP, HTTPS encrypts application data.

When session keys are available, Wireshark can decrypt HTTPS traffic and reveal:

- HTTP requests
- Responses
- URLs
- Headers
- Application data

Without decryption keys, only metadata such as IP addresses, ports, and TLS handshake information remains visible.

---

## Hunting Cleartext Credentials

SOC analysts should always search for credentials transmitted in plaintext.

Common protocols include:

- FTP
- HTTP
- Telnet
- POP3

Credential exposure may lead to:

- Unauthorized access
- Privilege escalation
- Lateral Movement

---

## Actionable Results

Traffic analysis should always produce actionable findings.

Examples include:

- Detecting reconnaissance activity.
- Identifying MITM attacks.
- Discovering malware communication.
- Detecting tunneling.
- Finding exposed credentials.
- Identifying compromised hosts.

The objective is not simply to observe traffic but to generate evidence that supports incident response.

---

# Key Takeaways

- Wireshark is a powerful network investigation tool.
- Reconnaissance activity can often be detected before exploitation.
- ARP Poisoning is a common technique used during MITM attacks.
- DNS and ICMP can be abused for covert communication.
- FTP and HTTP expose sensitive information because they are unencrypted.
- HTTPS protects data but can be analyzed when decryption material is available.
- Identifying exposed credentials is an important SOC investigation task.

---

# SOC Perspective

Traffic analysis is one of the most valuable skills for SOC analysts. During investigations, analysts use Wireshark to identify reconnaissance, Man-in-the-Middle attacks, tunneling activity, malware communication, credential exposure, and data exfiltration. Rather than inspecting every packet individually, analysts correlate filters, protocol behavior, and traffic patterns to reconstruct attacker activity and support incident response.
