# Objective

The objective of this room was to understand how web shells are used by attackers, identify indicators of web shell activity, analyze web server logs, correlate multiple data sources, and use network traffic analysis techniques to detect malicious behavior.

---

# Topics Covered

* Web Shell Fundamentals
* Web Log Analysis
* HTTP Request Analysis
* Auditd Monitoring
* Log Correlation
* Network Traffic Analysis
* Web Server Directories
* Threat Hunting Techniques

---

# Web Shell Indicators

## Unusual HTTP Methods and Request Patterns

Potential indicators include:

* Repeated GET requests in a short period of time
* POST requests to upload locations
* Continuous interaction with the same file
* Repeated requests to newly uploaded resources

These behaviors may indicate an attacker uploading or interacting with a web shell.

---

## Suspicious User-Agents

The User-Agent identifies the software making a request.

Indicators include:

* Modified User-Agents
* Outdated browsers
* Command-line tools such as:

  * curl
  * wget

These can help identify automated or malicious activity.

---

## Suspicious IP Addresses

Unexpected external IP addresses communicating with internal web services may indicate unauthorized access attempts.

---

## Suspicious Query Strings

Analysts should investigate:

* Unusually long query strings
* Parameters such as:

  * cmd=
  * exec=
* Encoded values
* Obfuscated commands

Encoded payloads often require additional analysis and decoding.

---

## Missing Referrer

The HTTP Referrer header shows where a user came from.

A missing referrer may indicate:

* Direct access
* Automated tools
* Web shell activity

However, analysts should remember that missing referrers can also occur during legitimate activity.

---

# Auditd Monitoring

Auditd is a Linux auditing framework used to track system activity.

Capabilities include:

* File monitoring
* Process monitoring
* Command execution tracking
* Audit trail generation

Auditd rules can be configured to monitor specific files, directories, or system actions.

---

# Correlating Web Logs and Auditd

Effective detection requires multiple data sources.

Examples:

* A suspicious POST request appears in web logs.
* Auditd records file creation activity.
* Auditd records command execution events.

By correlating these logs, analysts can reconstruct the attack timeline and validate suspicious behavior.

---

# Common Web Shell Locations

Typical locations include:

Apache:

* /var/www/html/

Nginx:

* /usr/share/nginx/html/

Other common locations:

* /uploads/
* /images/
* /admin/
* /tmp/

Misconfigured permissions may allow attackers to place malicious files in these directories.

---

# Threat Hunting Commands

Useful techniques include:

* Searching for recently modified files
* Identifying suspicious PHP files
* Searching for dangerous functions
* Looking for unusual file creation activity

Tools commonly used:

* find
* grep

---

# Network Traffic Analysis

Network traffic analysis provides visibility beyond log files.

Useful indicators include:

* Unusual HTTP methods
* Suspicious User-Agents
* Encoded payloads
* Command execution attempts
* Unexpected protocols
* Unexpected ports
* Abnormal resource usage

---

# Useful Wireshark Filters

Common filters include:

* http.request.method
* http.request.uri contains ".php"
* http.user_agent

These filters help identify suspicious requests and potentially malicious activity.

---

# Key Takeaways

* Web shells often leave traces in HTTP logs.
* Suspicious request patterns can indicate shell uploads or interaction.
* User-Agent analysis can reveal attacker tooling.
* Auditd provides valuable visibility into system activity.
* Correlating multiple log sources increases detection accuracy.
* Network traffic analysis can reveal attacker actions not visible in logs alone.

---

# SOC Perspective

As a SOC Analyst, detecting web shells requires monitoring web server logs, investigating suspicious HTTP requests, analyzing User-Agent strings, reviewing file creation activity, and correlating multiple log sources. Combining web logs, auditd events, and network traffic provides a more complete view of attacker activity and improves detection effectiveness.

---

# Personal Notes

Topics to review later:

* Auditd Rule Creation
* Web Shell Persistence Techniques
* Threat Hunting for Web Servers
* Apache & Nginx Logging
* Detection Engineering for Web Attacks
