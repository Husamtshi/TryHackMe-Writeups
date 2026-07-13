# Room: Wireshark: Packet Operations

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how to efficiently analyze packet captures using Wireshark statistics, protocol analysis, and display filters to identify suspicious network activity.

---

# Topics Covered

- Wireshark Statistics
- Protocol Statistics
- Packet Filtering
- Protocol Filters
- Advanced Display Filters
- Traffic Analysis

---

# Key Concepts

## Statistics Summary

The **Statistics** menu provides a high-level overview of captured traffic without manually inspecting every packet.

Useful information includes:

- Total packets
- Capture duration
- Traffic volume
- Conversations
- Endpoints
- Protocol hierarchy

Statistics help analysts quickly understand the overall network activity before performing deeper analysis.

---

## Protocol Statistics

Wireshark can summarize all protocols observed inside a PCAP.

Common protocols include:

- TCP
- UDP
- DNS
- HTTP
- HTTPS
- ICMP
- ARP

Protocol statistics help identify unusual or unexpected protocols during investigations.

---

## Packet Filtering Principles

Display filters reduce investigation time by showing only relevant packets.

Instead of scrolling through thousands of packets, analysts isolate the traffic related to their investigation.

Examples include filtering by:

- Protocol
- IP Address
- Port
- Packet Contents
- Packet Length

---

## Protocol Filters

Common protocol filters include:

```text
http
```

```text
dns
```

```text
tcp
```

```text
udp
```

```text
icmp
```

```text
arp
```

These filters allow analysts to quickly focus on a specific protocol during investigations.

---

## Advanced Filtering

Wireshark supports combining multiple filters using logical operators.

Common examples include:

```text
ip.addr == 192.168.1.10
```

```text
tcp.port == 80
```

```text
dns && ip.addr == 10.10.10.5
```

```text
http.request.method == "POST"
```

```text
frame.len > 1000
```

Advanced filters allow analysts to isolate highly specific traffic and significantly speed up investigations.

---

# Key Takeaways

- Statistics provide a quick overview of captured traffic.
- Protocol statistics help identify network behavior and anomalies.
- Display filters are one of the most important Wireshark features.
- Advanced filters enable precise packet analysis.
- Efficient filtering greatly reduces investigation time during incident response.

---

# SOC Perspective

SOC analysts rarely inspect every packet manually. Instead, they begin by reviewing traffic statistics to understand the overall capture, then apply protocol and display filters to isolate suspicious communications. Mastering Wireshark filtering techniques enables faster detection of malicious traffic, reduces analysis time, and improves the efficiency of network investigations.
