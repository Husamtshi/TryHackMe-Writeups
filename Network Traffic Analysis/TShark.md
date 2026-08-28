# Room: TShark

**Platform:** TryHackMe  
**Path:** SOC Level 1  
**Status:** Completed

---

# Objective

Learn how to use **TShark**, the command-line version of Wireshark, for packet capture, filtering, analysis, and automation.

TShark is useful for SOC analysts because packet data can be processed directly from the terminal and combined with tools such as `grep`, `awk`, `cut`, and `uniq`.

---

# Useful Command-Line Tools

| Tool | Purpose |
|---|---|
| `capinfos` | Display capture file information |
| `grep` | Search text |
| `cut` | Extract fields |
| `uniq` | Remove duplicate values |
| `nl` | Number output lines |
| `sed` | Process/modify text streams |
| `awk` | Search and process structured text |

A useful workflow is:

```text
TShark
  ↓
Filter / Extract
  ↓
grep / awk / cut
  ↓
Analyse Results
```

---

# TShark Basic Parameters

## Help and Version

Display help:

```bash
tshark -h
```

Show the installed version:

```bash
tshark -v
```

List available interfaces:

```bash
tshark -D
```

---

# Live Packet Capture

Capture traffic from an interface:

```bash
tshark -i 1
```

or:

```bash
tshark -i ens55
```

Without specifying an interface, TShark can sniff traffic similarly to `tcpdump`:

```bash
tshark
```

---

# Reading and Writing PCAP Files

Read an existing capture:

```bash
tshark -r demo.pcapng
```

Capture a specific number of packets:

```bash
tshark -c 10
```

Write captured traffic to a file:

```bash
tshark -w sample-capture.pcap
```

Display detailed packet information:

```bash
tshark -V
```

Suppress packet output:

```bash
tshark -q
```

Display packet bytes in hexadecimal and ASCII:

```bash
tshark -x
```

---

# Capture Conditions

Capture filters can control when TShark stops or how capture files are rotated.

## Autostop: `-a`

Stop after a specific duration:

```bash
tshark -w test.pcap -a duration:10
```

Stop after reaching a file size in KB:

```bash
tshark -w test.pcap -a filesize:10
```

Stop after creating a specific number of files:

```bash
tshark -w test.pcap -a filesize:10 -a files:3
```

---

# Ring Buffer: `-b`

`-b` is useful when continuously capturing traffic and rotating through multiple files.

Rotate after a specific duration:

```bash
tshark -w test.pcap -b duration:10
```

Rotate after reaching a file size:

```bash
tshark -w test.pcap -b filesize:10
```

Limit the number of files:

```bash
tshark -w test.pcap -b filesize:10 -b files:3
```

The oldest file is overwritten when the ring buffer reaches its configured limit.

---

# Capture Filters

Capture filters use **BPF syntax** and are applied while traffic is being captured.

The general format is:

```bash
tshark -f 'filter'
```

## Host

Capture traffic to or from a host:

```bash
tshark -f 'host 10.10.10.10'
```

## Network

Capture traffic from a network:

```bash
tshark -f 'net 10.10.10.0/24'
```

## Port

Capture traffic on a specific port:

```bash
tshark -f 'port 80'
```

Port range:

```bash
tshark -f 'portrange 80-100'
```

## Source and Destination

Source host:

```bash
tshark -f 'src host 10.10.10.10'
```

Destination host:

```bash
tshark -f 'dst host 10.10.10.10'
```

## Protocol

Capture TCP traffic:

```bash
tshark -f 'tcp'
```

Capture UDP traffic:

```bash
tshark -f 'udp'
```

Capture ICMP:

```bash
tshark -f 'ip proto 1'
```

Capture a specific MAC address:

```bash
tshark -f 'ether host F8:DB:C5:A2:5D:81'
```

---

# Capture Filter vs Display Filter

This is an important distinction.

### Capture Filter

Uses:

```bash
-f
```

It filters traffic **during capture**.

Example:

```bash
tshark -f 'port 80'
```

### Display Filter

Uses:

```bash
-Y
```

It filters packets **after they are captured or when reading a PCAP**.

Example:

```bash
tshark -r demo.pcapng -Y 'tcp.port == 80'
```

Remember:

```text
-f  → Capture Filter
-Y  → Display Filter
```

---

# Display Filters

Display filters use Wireshark's display-filter syntax.

## IP

Filter an IP address:

```bash
tshark -Y 'ip.addr == 10.10.10.10'
```

Filter a network:

```bash
tshark -Y 'ip.addr == 10.10.10.0/24'
```

Source IP:

```bash
tshark -Y 'ip.src == 10.10.10.10'
```

Destination IP:

```bash
tshark -Y 'ip.dst == 10.10.10.10'
```

---

# TCP Filters

Filter TCP traffic:

```bash
tshark -Y 'tcp'
```

Filter a TCP port:

```bash
tshark -Y 'tcp.port == 80'
```

Filter a source TCP port:

```bash
tshark -Y 'tcp.srcport == 80'
```

---

# HTTP Filters

Display HTTP traffic:

```bash
tshark -Y 'http'
```

Filter HTTP responses with status code 200:

```bash
tshark -Y 'http.response.code == 200'
```

---

# DNS Filters

Display DNS traffic:

```bash
tshark -Y 'dns'
```

Display DNS A-record queries:

```bash
tshark -Y 'dns.qry.type == 1'
```

DNS analysis can be useful for identifying suspicious domains, C2 communication, and possible DNS tunnelling or exfiltration.

---

# Useful Analysis Workflow

A practical workflow for analysing a PCAP is:

```text
PCAP
 ↓
capinfos
 ↓
Identify protocols / hosts
 ↓
Apply display filters
 ↓
Extract useful fields
 ↓
grep / awk / cut / uniq
 ↓
Investigate suspicious traffic
```

For example, first inspect the capture:

```bash
capinfos capture.pcapng
```

Then identify DNS traffic:

```bash
tshark -r capture.pcapng -Y 'dns'
```

Extract DNS query names:

```bash
tshark -r capture.pcapng -Y 'dns' -T fields -e dns.qry.name
```

Remove duplicates:

```bash
tshark -r capture.pcapng -Y 'dns' -T fields -e dns.qry.name | sort | uniq
```

Count repeated queries:

```bash
tshark -r capture.pcapng -Y 'dns' -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```

---

# SOC Analyst Perspective

TShark is especially useful when a GUI is not available or when packet analysis needs to be automated.

A SOC analyst can use it to:

- Identify communicating hosts
- Investigate suspicious IP addresses
- Analyse DNS requests
- Investigate HTTP traffic
- Filter specific ports
- Extract packet fields
- Process large PCAP files
- Automate repetitive investigations

The biggest advantage is that TShark output can be piped into other command-line tools.

Example:

```bash
tshark -r capture.pcapng -Y 'dns' -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```

---

# Key Takeaways

- TShark is the command-line version of Wireshark.
- `-D` lists available capture interfaces.
- `-i` selects an interface.
- `-r` reads a PCAP file.
- `-w` writes captured traffic to a file.
- `-V` displays detailed packet information.
- `-x` displays packet bytes.
- `-q` suppresses packet output.
- `-c` limits the number of packets.
- `-a` provides autostop conditions.
- `-b` provides ring-buffer capture options.
- `-f` is used for capture filters.
- `-Y` is used for display filters.
- `capinfos` is useful for getting an initial overview of a capture.
- Combining TShark with `grep`, `awk`, `cut`, and `uniq` makes large packet investigations easier to automate.

---

# TShark Cheat Sheet

```bash
# Interfaces
tshark -D

# Live capture
tshark -i 1

# Read PCAP
tshark -r capture.pcapng

# Write capture
tshark -w capture.pcap

# Capture 10 packets
tshark -c 10

# Detailed packets
tshark -V

# Packet bytes
tshark -x

# Capture filter
tshark -f 'host 10.10.10.10'

# Display filter
tshark -Y 'ip.addr == 10.10.10.10'

# HTTP
tshark -Y 'http'

# DNS
tshark -Y 'dns'

# Extract DNS queries
tshark -r capture.pcapng -Y 'dns' -T fields -e dns.qry.name

# Count unique DNS queries
tshark -r capture.pcapng -Y 'dns' -T fields -e dns.qry.name | sort | uniq -c | sort -nr
```
