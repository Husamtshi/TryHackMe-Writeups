# Room: Linux Threat Detection 1

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how attackers gain Initial Access to Linux systems through SSH, exposed services, and human mistakes. Understand how SOC analysts use authentication logs, application logs, Auditd, and process tree analysis to detect and investigate Linux compromises.

---

# Topics Covered

- Initial Access
- SSH Authentication
- Application Logs
- Web Attacks
- Process Tree Analysis
- Auditd
- Human-Led Attacks
- Threat Hunting

---

# Key Concepts

## Initial Access via SSH

SSH is one of the most common remote administration services for Linux systems and is frequently targeted by attackers.

Common attack methods include:

- Stolen SSH private keys
- Weak passwords
- Internet-exposed SSH services

Common risks include:

- Private SSH keys exposed in Git repositories or automation servers.
- SSH keys stolen from compromised administrator workstations.
- Weak or default passwords.
- Misconfigured or publicly exposed SSH servers.

> **Note:** The keyword **publickey** in SSH authentication logs indicates that the user authenticated using **key-based authentication** rather than a password.

---

## Authentication Logs

SSH authentication activity is primarily recorded in:

```bash
/var/log/auth.log
```

SOC analysts should investigate:

- Successful logins
- Failed logins
- Brute-force attempts
- Unexpected source IP addresses
- Unusual login times
- Authentication method (Password vs Public Key)

Authentication logs are often the first place to investigate an SSH compromise.

---

## Application Logs

Application logs provide visibility into activities occurring inside individual services.

Examples include:

- Web server logs
- Database logs
- VPN logs
- Mail server logs

Application logs rarely reveal the complete attack but often contain valuable indicators of compromise.

---

## Web Application Initial Access

Public-facing web applications are one of the most common Linux attack vectors.

Applications vulnerable to Command Injection or Remote Code Execution may allow attackers to execute arbitrary system commands.

SOC analysts should review web server logs for:

- Suspicious HTTP requests
- Unexpected URL parameters
- Command Injection attempts
- Unusual user behavior

---

## Process Tree Analysis

Process tree analysis is one of the most effective investigation techniques available to SOC analysts.

Instead of analyzing a suspicious command alone, analysts trace the parent-child process relationships to identify where the execution originated.

Example:

```text
systemd
└── python (Web Application)
    └── bash
        └── whoami
```

This helps determine whether the command originated from:

- A legitimate administrator
- A compromised web application
- A malicious script
- An attacker's SSH session

---

## Auditd Investigation

Auditd provides detailed runtime visibility into Linux process execution.

A common investigation workflow includes:

- Searching for a suspicious process using `ausearch`
- Identifying its Process ID (PID)
- Tracing parent processes until reaching the original application
- Investigating additional child processes executed by the same parent

This approach allows analysts to reconstruct the attack chain and identify follow-on attacker activity.

---

## Human-Led Attacks

Although phishing is less common on Linux servers, human error remains an important attack vector.

Examples include:

- Executing untrusted shell scripts downloaded from the Internet.
- Installing malicious software packages due to typos (Typosquatting).
- Running commands copied from untrusted forums or websites.

These attacks often bypass traditional security controls because they rely on user actions rather than software vulnerabilities.

---

# Key Takeaways

- SSH remains one of the most common Linux Initial Access vectors.
- Authentication logs are essential when investigating SSH compromises.
- Application logs provide valuable context during web application investigations.
- Process tree analysis is one of the strongest techniques for identifying the source of suspicious activity.
- Auditd helps reconstruct attack chains by recording detailed process execution events.
- Human mistakes and supply-chain attacks remain significant risks on Linux systems.

---

# SOC Perspective

Effective Linux investigations require correlating multiple sources of evidence rather than relying on a single log. Authentication logs identify who accessed the system, application logs reveal attacks against exposed services, and Auditd provides detailed visibility into process execution. By combining these sources and building process trees, SOC analysts can determine how attackers gained Initial Access and quickly identify malicious activity before it progresses further into the attack lifecycle.
