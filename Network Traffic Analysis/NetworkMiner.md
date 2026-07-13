# Room: NetworkMiner

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how to use NetworkMiner for Network Forensics by analyzing packet capture (PCAP) files and extracting useful evidence without manually inspecting every packet.

---

# Topics Covered

- Network Forensics
- NetworkMiner Overview
- Host Identification
- File Extraction
- Credential Analysis
- Image Extraction
- Session Reconstruction
- PCAP Analysis

---

# Key Concepts

## What is NetworkMiner?

NetworkMiner is a Network Forensics Analysis Tool (NFAT) used to analyze PCAP files.

Unlike Wireshark, which focuses on packet-level analysis, NetworkMiner automatically extracts artifacts from captured network traffic, making investigations faster and easier.

---

## Why NetworkMiner?

NetworkMiner helps analysts quickly identify:

- Hosts
- Sessions
- Operating Systems
- Open Ports
- Transferred Files
- Images
- Credentials
- DNS Queries
- Certificates

without manually inspecting thousands of packets.

---

## Host Information

One of the first tabs analysts investigate is the **Hosts** tab.

It provides information such as:

- IP Addresses
- MAC Addresses
- Host Names
- Operating Systems
- Communication Partners

This helps identify all devices involved in the captured traffic.

---

## File Extraction

NetworkMiner can automatically recover transferred files from network traffic.

Examples include:

- Images
- PDF documents
- ZIP archives
- Executables
- Office documents

This feature is especially useful during malware investigations and data exfiltration analysis.

---

## Credential Analysis

If network traffic contains unencrypted authentication data, NetworkMiner can extract:

- Usernames
- Passwords
- NTLM Authentication
- HTTP Credentials

This helps identify compromised accounts during investigations.

---

## Image Extraction

NetworkMiner can reconstruct and display images transferred over the network.

This allows analysts to quickly identify downloaded or uploaded media without manually rebuilding HTTP sessions.

---

## Session Reconstruction

Instead of inspecting every packet individually, NetworkMiner reconstructs communication sessions.

Analysts can easily understand:

- Client-Server communication
- HTTP Requests
- DNS Activity
- File Transfers

This significantly reduces investigation time.

---

## Version Differences

The room also introduces the differences between the Free and Professional versions of NetworkMiner.

The Professional edition includes additional capabilities for larger investigations and more advanced forensic analysis.

---

# Key Takeaways

- NetworkMiner is designed for Network Forensics.
- It analyzes PCAP files without requiring deep packet-by-packet inspection.
- It automatically extracts hosts, files, images, credentials, and other artifacts.
- Session reconstruction makes investigations much faster.
- NetworkMiner complements Wireshark rather than replacing it.

---

# SOC Perspective

NetworkMiner is an excellent triage tool during incident response. SOC analysts often use it alongside Wireshark: NetworkMiner quickly extracts hosts, files, credentials, and transferred objects from a PCAP, while Wireshark is used later for detailed packet-level analysis. Combining both tools provides faster investigations and better visibility into attacker activity.
