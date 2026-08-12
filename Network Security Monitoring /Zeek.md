# Room: Zeek

**Platform:** TryHackMe
**Path:** SOC Level 1
**Status:** Completed

---

# Objective

Learn how Zeek analyzes network traffic and generates logs for SOC investigations.

---

# Zeek

Zeek is a **Network Security Monitoring (NSM)** tool used for:

* Network visibility
* Threat Hunting
* Traffic analysis
* Incident investigation

**Main idea:**

`Snort → signatures / known attacks`

`Zeek → network visibility / detailed logs`

---

# Important Logs

| Log          | Purpose             |
| ------------ | ------------------- |
| `conn.log`   | Network connections |
| `dns.log`    | DNS activity        |
| `http.log`   | HTTP traffic        |
| `ssl.log`    | TLS/SSL             |
| `ssh.log`    | SSH                 |
| `files.log`  | File activity       |
| `notice.log` | Suspicious activity |

**UID** is used to correlate the same connection across different Zeek logs.

---

# PCAP Analysis

Process a PCAP:

```bash
zeek -r sample.pcap
```

Useful options:

```text
-r  → read PCAP
-C  → ignore checksum errors
-v  → show version
```

---

# Log Analysis

Useful Linux commands:

```bash
grep
cat
cut
sort
uniq
```

Zeek-specific:

```bash
zeek-cut
```

Example:

```bash
cat conn.log | zeek-cut uid id.orig_h id.resp_h
```

---

# Zeek Signatures

Signatures detect specific network patterns.

```bash
zeek -C -r sample.pcap -s sample.sig
```

Results can appear in:

```text
signatures.log
notice.log
```

---

# Zeek Scripts

Zeek uses an event-driven scripting language.

Scripts use:

```text
.zeek
```

Scripts can be used to:

* Detect events
* Correlate activity
* Automate analysis
* Create custom detections

---

# SOC Workflow

```text
PCAP
 ↓
Zeek Logs
 ↓
conn.log
 ↓
Suspicious IP / Domain
 ↓
UID
 ↓
DNS / HTTP / SSL / SSH
 ↓
Build Timeline
```

---

# Key Takeaways

* Zeek = **network visibility + analysis**
* Start with `conn.log`
* Use **UID** to correlate logs
* Know the main logs rather than memorizing all 50+
* `zeek-cut` is useful for extracting fields
* Zeek scripts allow custom detection and automation
