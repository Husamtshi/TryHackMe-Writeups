# Snort Room

## Objective

The objective of this room was to understand the fundamentals of Snort as a Network Intrusion Detection System (NIDS) and Intrusion Prevention System (IPS), learn how Snort rules work, and analyze network traffic for suspicious activity.

---

## Topics Covered

* Introduction to Snort
* IDS vs IPS
* Snort Rule Structure
* Rule Headers
* Rule Options
* Content Matching
* Packet Inspection
* Logging and Alerting
* Basic Linux log searching with `grep`

---

## Key Concepts

### IDS vs IPS

* **IDS (Intrusion Detection System)** monitors network traffic and generates alerts when suspicious activity is detected.
* **IPS (Intrusion Prevention System)** can detect and actively block malicious traffic.

---

### Snort Rule Structure

A Snort rule consists of two main parts:

* Rule Header
* Rule Options

Example:

```snort
alert tcp any any -> any 80 (msg:"HTTP Traffic"; sid:100001;)
```

---

### Important Rule Keywords

| Keyword | Purpose                                            |
| ------- | -------------------------------------------------- |
| alert   | Generate an alert                                  |
| msg     | Alert description                                  |
| content | Search for specific data inside the packet payload |
| sid     | Unique Rule Identifier                             |
| rev     | Rule revision number                               |

---

### Understanding `content`

The `content` option allows Snort to search for specific strings or signatures inside network packets.

Example:

```snort
content:"GIF89a";
```

This detects packets containing the GIF file signature.

---

### Interface Selection

```bash
snort -i eth0
```

Monitor a single network interface.

```bash
snort -i eth0:eth1 -Q --daq afpacket
```

Run Snort in Inline IPS mode between two interfaces using AF_PACKET.

---

### Useful Linux Commands

Search logs:

```bash
grep "alert" snort.log
```

Ignore uppercase/lowercase:

```bash
grep -i "gif" snort.log
```

Show three lines after a match:

```bash
grep -A 3 "alert" snort.log
```

---

## Key Takeaways

* Snort can operate as both IDS and IPS.
* Detection is based on customizable rules.
* The `content` keyword searches packet payloads.
* `-q` runs Snort quietly.
* `-Q` enables Inline IPS mode.
* `--daq afpacket` uses the Linux AF_PACKET packet acquisition module.
* `grep` is useful for searching Snort log files efficiently.

---

## SOC Perspective

As a SOC Analyst, understanding Snort helps identify malicious network activity, investigate alerts, validate suspicious traffic, and improve detection capabilities by creating or tuning detection rules.

---

## Personal Notes

Things I want to review later:

* Advanced Snort Rule Writing
* Flow and Flowbits
* PCRE Rules
* Detection Optimization
* Snort Performance Tuning

