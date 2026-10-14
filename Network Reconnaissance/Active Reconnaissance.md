# Room: Active Reconnaissance

**Platform:** TryHackMe  
**Path:** Jr Penetration Tester  
**Status:** Completed

---

# Objective

Learn to interact directly with a target during authorized reconnaissance to check reachability, understand network paths, identify exposed services, and gather information about web technologies and service banners.

---

# Topics Covered

- Browser Developer Tools and extensions
- Ping and ICMP
- TTL clues
- Traceroute and MTR
- Telnet
- Netcat
- Banner grabbing
- TCP connectivity testing
- HTTP headers
- IPv4 and IPv6 commands

---

# Browser-Based Reconnaissance

A browser and its Developer Tools can reveal useful information while manually inspecting a web application.

| Operating System | Developer Tools Shortcut |
|---|---|
| Linux / Windows | `Ctrl + Shift + I` |
| macOS | `Option + Command + I` |

Useful areas include:
- **Network:** requests, responses, status codes, and headers
- **Sources / Debugger:** JavaScript and client-side code
- **Storage / Application:** cookies and browser storage
- **Security:** certificate and connection details

## Browser Extensions

- **FoxyProxy:** switches between proxies such as Burp Suite, ZAP, and SOCKS5 proxies.
- **User-Agent Switcher and Manager:** changes the User-Agent to test how a site responds to different browser or device identities. This changes a header; it does not make the browser or device genuinely different.
- **Wappalyzer:** identifies technologies that may be used by a site, such as CMS platforms, web servers, JavaScript frameworks, analytics tools, and CDNs.

Use extensions only within an authorized engagement. Technology detection is indicative and should be verified where possible.

---

# Ping and Host Reachability

`ping` sends ICMP Echo Requests and reports replies, latency, and packet loss.

```bash
# Linux / macOS
ping -c 10 MACHINE_IP

# Windows
ping -n 10 MACHINE_IP

# IPv6
ping -6 MACHINE_IPV6
```

## Interpreting Results

| Result | Possible Meaning | Next Step |
|---|---|---|
| Fast replies with little packet loss | Host responds to ICMP | Continue with authorized enumeration |
| Destination Host Unreachable | No route or a network/device reports it cannot reach the host | Check routing and target status |
| 100% packet loss | ICMP may be filtered, the host may be down, or a route may be unavailable | Try other authorized discovery methods |
| High latency or packet loss | Congestion, distance, unstable connectivity, or filtering | Inspect the path with traceroute / MTR |

**Important:** no ping response does not prove that a host is offline. Firewalls commonly block ICMP.

TTL values can sometimes provide clues about the operating system or network path, but they are not definitive OS identification.

---

# Traceroute and MTR

`traceroute` attempts to reveal the routers (hops) between your system and the destination. It can help identify routing paths, latency, and points where responses stop.

```bash
# Linux
traceroute MACHINE_IP

# Windows
tracert MACHINE_IP

# IPv6
traceroute -6 MACHINE_IPV6

# Continuous path and loss statistics
mtr MACHINE_IP
```

Additional modes, where supported:

```bash
# TCP probes
traceroute -T MACHINE_IP

# ICMP probes
traceroute -I MACHINE_IP
```

A missing hop does not necessarily mean traffic stops there; routers may forward packets while filtering traceroute responses. Results depend on the operating system and traceroute implementation.

---

# Telnet and Banner Grabbing

Telnet was designed for remote terminal access, but it sends data in cleartext and should not be used for secure administration. **SSH** is the secure alternative.

For reconnaissance, a Telnet client can sometimes connect to a TCP port and display a service's initial banner. Banners may reveal software names or versions.

```bash
telnet MACHINE_IP PORT
```

Telnet does not negotiate TLS, so it is unsuitable for directly inspecting encrypted HTTPS services. For HTTPS, use tools such as:

```bash
curl -I https://MACHINE_IP
openssl s_client -connect MACHINE_IP:443
```

A banner is only a clue: it may be hidden, customized, or inaccurate.

---

# Netcat

Netcat (`nc`) is a general-purpose networking tool that can connect to ports or listen for incoming TCP connections. It is useful for basic connectivity checks and banner grabbing.

## Connect to a TCP Port

```bash
nc -nv MACHINE_IP PORT
```

## Start a Listener

On the listening system:

```bash
nc -lvnp 1234
```

On the client system:

```bash
nc MACHINE_IP 1234
```

Once connected, text sent from one side can be received by the other.

| Option | Meaning |
|---|---|
| `-l` | Listen for an incoming connection |
| `-p` | Specify the local port |
| `-n` | Avoid DNS lookups; use numeric addresses |
| `-v` | Verbose output |
| `-vv` | More verbose output |
| `-k` | Keep listening after a client disconnects, if supported |
| `-6` | Use IPv6, if supported |

Flag syntax differs between Netcat implementations, so check `nc -h` or the local manual. Listening on ports below 1024 generally requires elevated privileges.

For TLS-enabled services, use an appropriate TLS-capable client such as `openssl s_client` or `ncat --ssl`.

---

# HTTP Headers and Service Information

For HTTP services, `curl` can inspect response headers:

```bash
curl -I http://MACHINE_IP
curl -I https://MACHINE_IP
```

Headers may disclose the server type, redirects, cookies, caching behaviour, and other useful clues. Modern servers may hide or modify version information, so headers should not be treated as definitive proof of the underlying software.

---

# Quick Reference

| Purpose | Command |
|---|---|
| Ping on Linux / macOS | `ping -c 10 MACHINE_IP` |
| Ping on Windows | `ping -n 10 MACHINE_IP` |
| Ping IPv6 | `ping -6 MACHINE_IPV6` |
| Trace route on Linux | `traceroute MACHINE_IP` |
| Trace route on Windows | `tracert MACHINE_IP` |
| Trace IPv6 route | `traceroute -6 MACHINE_IPV6` |
| Monitor path continuously | `mtr MACHINE_IP` |
| TCP traceroute | `traceroute -T MACHINE_IP` |
| Connect to a TCP port | `nc -nv MACHINE_IP PORT` |
| Connect using Telnet | `telnet MACHINE_IP PORT` |
| Start a Netcat listener | `nc -lvnp PORT` |
| Inspect HTTP headers | `curl -I http://MACHINE_IP` |
| Inspect HTTPS certificate / handshake | `openssl s_client -connect MACHINE_IP:443` |

---

# Active Reconnaissance Workflow

```text
Confirm Scope and Target
        ↓
Inspect the Web Application
        ↓
Use Developer Tools / Identify Technologies
        ↓
Check Reachability with Ping
        ↓
Inspect the Network Path with Traceroute
        ↓
Probe Authorized TCP Ports
        ↓
Grab Service Banners / Inspect HTTP Headers
        ↓
Document Findings
        ↓
Proceed to Structured Scanning (e.g., Nmap)
```

---

# Key Takeaways

- **Active reconnaissance interacts with the target.** It can be logged and may trigger security monitoring.
- **Ping is not definitive.** ICMP filtering can make an active host appear unresponsive.
- **Traceroute helps explain the path**, but filtered hops do not necessarily indicate a broken route.
- **Netcat and Telnet can test TCP connectivity** and sometimes reveal service banners.
- **Telnet is not secure for remote administration** because it transmits data in cleartext.
- **Use TLS-capable tools for encrypted services.** Plain Telnet cannot inspect HTTPS traffic.
- **Browser tools reveal useful clues.** Headers, JavaScript, cookies, and technology fingerprints can guide further testing.
- **Document and verify findings.** A banner, header, TTL, or extension result is a lead—not conclusive proof.

---

# Pentester Perspective

Active reconnaissance builds on passive reconnaissance by interacting directly with authorized targets. Start with simple checks, observe how the target responds, and use those results to decide what to investigate next.

The goal is not to run every command blindly. It is to understand what each command tests, interpret the result carefully, and record useful evidence before moving to more comprehensive scanning with Nmap.
