# Room: Linux Logging for SOC

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn the primary Linux log sources used by SOC analysts, understand authentication monitoring, application logs, Bash history, system calls, and Auditd for Linux threat detection and incident investigations.

---

# Topics Covered

- Linux Log Files
- Authentication Logs
- SSH Monitoring
- System Logs
- Application Logs
- Bash History
- System Calls
- Auditd
- Threat Hunting

---

# Key Concepts

## Linux Log Files

Most Linux logs are stored under:

```bash
/var/log/
```

Unlike Windows Event Logs, many Linux logs are plain text, making them easy to search using Linux commands such as:

- `cat`
- `grep`
- `less`
- `tail`

---

## Authentication Logs

One of the most important log sources for SOC analysts is:

```bash
/var/log/auth.log
```

> On RHEL-based systems, the equivalent log is `/var/log/secure`.

Authentication logs record:

- User logins
- User logouts
- SSH authentication
- `sudo` usage
- `su` usage
- User management events

Common investigations include:

- Successful logins
- Failed SSH logins
- Brute-force attempts
- Privilege escalation using `sudo`

---

## SSH Monitoring

SSH activity is recorded inside **auth.log**.

Analysts should investigate:

- Successful (`Accepted`) logins
- Failed (`Failed`) logins
- Unknown source IP addresses
- Multiple failed authentication attempts
- Logins outside normal working hours

---

## User Management Events

Authentication logs also record administrative actions such as:

- User creation
- User deletion
- Password changes
- Group modifications

Common commands appearing in logs include:

- `useradd`
- `usermod`
- `userdel`
- `passwd`

These events may indicate persistence or privilege escalation.

---

## Generic System Logs

Other useful Linux log files include:

- `/var/log/syslog`
- `/var/log/kern.log`
- `/var/log/messages`
- `/var/log/dpkg.log`
- `/var/log/apt`
- `/var/log/yum.log`
- `/var/log/dnf.log`

These logs are valuable during Digital Forensics and Incident Response (DFIR), although they tend to be noisier than authentication logs.

---

## Application Logs

Many Linux services maintain dedicated log files.

Examples include:

- Nginx Access Logs
- Apache Logs
- Database Logs
- Mail Server Logs
- Container Logs

Application logs are essential when investigating attacks targeting specific services.

---

## Bash History

Each Linux user has a Bash history file, typically located at:

```bash
~/.bash_history
```

It records previously executed commands and can provide useful investigation evidence.

Useful command:

```bash
sudo grep -i "whoami" /root/.bash_history
```

Bash History limitations include:

- Commands beginning with a space may not be recorded.
- Commands executed from scripts are not individually logged.
- Other shells (such as `/bin/sh`) may bypass Bash history.
- Attackers may delete or modify the history file.

Because of these limitations, Bash history should never be the only source of evidence.

---

## System Calls

System calls represent communication between applications and the Linux kernel.

Examples include:

- `execve`
- `open`
- `read`
- `write`

Most EDR solutions and Linux monitoring tools rely on system calls because nearly every system action eventually reaches the kernel.

---

## Auditd

Auditd (Audit Daemon) provides advanced runtime monitoring for Linux systems.

Instead of logging every system activity, Auditd monitors specific system calls based on configurable rules.

Useful information recorded includes:

- Process ID (PID)
- Parent Process ID (PPID)
- Executed binary
- Logged-in user (AUID)
- Current working directory (CWD)
- Executing user (UID)
- Audit rule key

The `ausearch` utility simplifies searching and filtering Auditd events.

Auditd is especially useful for monitoring:

- Process execution
- File modifications
- Sensitive configuration files
- Administrative actions
- Security-critical system calls

---

# Key Takeaways

- Linux logs are primarily stored under `/var/log/`.
- Authentication logs are among the most valuable log sources for SOC analysts.
- SSH activity should always be monitored for suspicious authentication attempts.
- Application-specific logs provide valuable context during investigations.
- Bash history is useful but should not be fully trusted.
- Auditd offers detailed runtime visibility through system call monitoring.
- Correlating multiple Linux log sources provides a more complete picture during incident investigations.

---

# SOC Perspective

Linux investigations require correlating multiple log sources rather than relying on a single file. Authentication logs identify who accessed the system, application logs reveal attacker interaction with services, Bash history may provide supporting evidence, and Auditd offers detailed visibility into process execution and file modifications. Together, these sources allow SOC analysts to reconstruct attacker activity, detect persistence techniques, and investigate Linux compromises more effectively.
