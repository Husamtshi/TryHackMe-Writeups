# Room: Passive Reconnaissance

**Platform:** TryHackMe  
**Path:** Jr Penetration Tester  
**Status:** Completed

---

# Objective

Learn how to collect publicly available information about a target before directly interacting with its systems. Passive reconnaissance helps identify domain details, DNS records, subdomains, and internet-facing assets while minimizing direct contact with the target.

---

# Topics Covered

- Passive reconnaissance
- WHOIS and RDAP
- DNS records
- `nslookup` and `dig`
- Subdomain discovery
- DNSDumpster
- Certificate Transparency
- `crt.sh`
- Shodan
- Attack surface mapping

---

# Passive Reconnaissance

Passive reconnaissance collects information from public sources without directly probing the target's infrastructure.

It can help identify domain registration details, name servers, mail servers, public DNS records, subdomains revealed by certificates, and internet-facing services indexed by third-party platforms.

Passive does not mean every action is invisible or risk-free. Queries to third-party services may be logged, and testing must remain within the authorized scope.

---

# WHOIS and RDAP

WHOIS provides domain registration information such as registrar, registration and expiry dates, name servers, domain status, and registrant details when not redacted.

Modern domain lookup increasingly uses **RDAP** (Registration Data Access Protocol). Personal information is often hidden due to privacy protections.

If the local `whois` command behaves unexpectedly, use these alternatives:

- WHOIS: https://whois.icann.org/
- ICANN Lookup / RDAP: https://lookup.icann.org/
- Historical WHOIS snapshots: https://www.whoxy.com/ (free access is limited)

Example:

```bash
whois tryhackme.com
```

---

# DNS Enumeration

DNS records reveal how a domain's services are configured.

| Record | Purpose |
|---|---|
| A | Maps a name to an IPv4 address |
| AAAA | Maps a name to an IPv6 address |
| MX | Identifies mail servers |
| TXT | Stores text such as SPF, domain verification, and other policy data |
| NS | Identifies authoritative name servers |
| CNAME | Aliases one domain name to another |

## `nslookup` vs `dig`

Both tools query DNS. `nslookup` is useful for compatibility and is common on Windows systems. `dig` is generally preferred for detailed output, TTL information, flexible record queries, and scripting.

```bash
# A record
nslookup -type=A tryhackme.com
dig tryhackme.com A

# MX record using a chosen DNS resolver
nslookup -type=MX tryhackme.com 1.1.1.1
dig @1.1.1.1 tryhackme.com MX

# TXT record
nslookup -type=TXT tryhackme.com
dig tryhackme.com TXT

# AAAA record
dig tryhackme.com AAAA
```

**TTL** (Time To Live) indicates how long a DNS response may be cached.

Standard DNS lookups resolve names you already know; they do not automatically discover every subdomain.

---

# Passive Subdomain Discovery

## DNSDumpster

DNSDumpster aggregates publicly available DNS information and presents relationships between domains, subdomains, IP addresses, and infrastructure in a visual format.

Website: https://dnsdumpster.com/

It can help reveal subdomains that are not obvious from the main website. Its results depend on the sources available to the service, so it should not be treated as a complete inventory.

## Certificate Transparency and `crt.sh`

Certificate Transparency (CT) logs publicly record issued TLS certificates. Certificates may contain multiple names in the **Subject Alternative Name (SAN)** field.

Searching certificate records can reveal additional subdomains without directly scanning the target.

Website: https://crt.sh/

Example search pattern:

```text
%.tryhackme.com
```

This can reveal hostnames that do not appear in normal DNS lookups. A hostname appearing in a certificate does not guarantee that it is currently active.

---

# Shodan

Shodan is a search engine for internet-connected devices and services. It indexes information gathered by its own crawlers, including service banners, exposed ports, and details about internet-facing systems.

Website: https://www.shodan.io/

Shodan can help identify:
- Publicly exposed services
- Service banners and versions
- Hosting and network information
- Potentially outdated or misconfigured internet-facing assets

Shodan results are third-party observations and may be stale. Verify findings only through authorized methods and within the engagement scope.

---

# Command Quick Reference

| Purpose | Example |
|---|---|
| WHOIS registration lookup | `whois tryhackme.com` |
| Query A record | `dig tryhackme.com A` |
| Query AAAA record | `dig tryhackme.com AAAA` |
| Query MX record | `dig @1.1.1.1 tryhackme.com MX` |
| Query TXT record | `dig tryhackme.com TXT` |
| Query NS records | `dig tryhackme.com NS` |
| Legacy A-record lookup | `nslookup -type=A tryhackme.com` |
| Legacy MX lookup | `nslookup -type=MX tryhackme.com 1.1.1.1` |
| Passive subdomain discovery | Search `%.tryhackme.com` on `crt.sh` |

---

# Passive Reconnaissance Workflow

```text
Target Domain
      ↓
WHOIS / RDAP
      ↓
DNS Records
      ↓
DNSDumpster
      ↓
Certificate Transparency (crt.sh)
      ↓
Shodan
      ↓
Consolidate Subdomains, IPs, and Services
      ↓
Validate Findings Within Scope
```

---

# Key Takeaways

- **Start with public information.** Domain registration, DNS, certificates, and indexed services can reveal useful clues before active testing.
- **Use `dig` as the default DNS tool** for detailed queries and scripting; keep `nslookup` for compatibility.
- **DNS lookups are not subdomain enumeration.** They resolve known names, while DNSDumpster and CT logs can help discover additional names.
- **Certificate Transparency is valuable.** SAN entries can reveal hostnames associated with a domain.
- **Shodan indexes services, not websites alone.** It can provide a view of exposed infrastructure.
- **Validate and document findings.** Public data may be incomplete or outdated, and testing must remain within the authorized scope.

---

# Pentester Perspective

Passive reconnaissance builds the initial map of a target's public attack surface. The goal is to gather useful leads before moving to active enumeration.

A practical workflow is to collect the domain's registration details, query its DNS records, search for subdomains through public DNS and certificate data, and review third-party indexes such as Shodan. Then organize the results into a list of domains, IP addresses, and services for authorized follow-up testing.
