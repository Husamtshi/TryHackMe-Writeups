# Room: Linux Threat Detection 2

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how attackers perform Discovery after gaining Initial Access, transfer additional tools to compromised Linux systems, and how SOC analysts detect these activities using Auditd, authentication logs, process tree analysis, network traffic, and endpoint telemetry.

---

# Topics Covered

- Discovery
- Specialized Discovery
- Ingress Tool Transfer
- Process Tree Analysis
- Auditd
- Authentication Logs
- Network Traffic Analysis
- Malware Case Study (Dota3)

---

# Key Concepts

## Initial Discovery

Once attackers gain access to a Linux system, they usually begin by gathering basic information about the host.

Common Discovery goals include:

- Operating system information
- Hostname
- Current user
- Installed software
- Running processes
- Network configuration
- User accounts and privileges

Common commands:

```bash
pwd
ls /
uname -a
hostname
env
whoami
id
ps aux
ip a
ss -tnlp
```

> **SOC Note:** The `whoami` command is frequently executed immediately after a successful compromise and can be a valuable detection opportunity.

---

## Specialized Discovery

After basic reconnaissance, attackers perform more targeted discovery depending on their objective.

### Credential Hunting

Examples:

```bash
history | grep pass
find / -name ".env"
find /home -name "id_rsa"
```

Attackers search for:

- Passwords
- SSH private keys
- Environment files
- Sensitive configuration files

---

### System Capability Discovery

Cryptominers often inspect hardware resources before deployment.

Common commands:

```bash
cat /proc/cpuinfo
lscpu
free -m
top
htop
```

The goal is to determine whether the system is suitable for cryptocurrency mining.

---

### Internal Network Discovery

Attackers frequently scan internal networks to identify additional victims.

Examples:

```bash
ping <IP>
nc
```

This activity may indicate preparation for lateral movement.

---

## "Hack and Forget" Attacks

Some attacks are fully automated and focus on immediate financial gain.

Typical objectives include:

- Installing cryptocurrency miners
- Enrolling systems into botnets
- Using compromised hosts as proxies

These attacks usually perform only a brief discovery phase before deploying malware.

---

## Ingress Tool Transfer

Attackers often download additional tools after gaining initial access.

Common Linux utilities include:

### Wget

```bash
wget <URL>
```

Downloads malware or scripts from remote servers.

---

### Curl

```bash
curl <URL>
```

Downloads files or communicates with attacker infrastructure.

---

### SCP / SFTP

```bash
scp attacker@server:file .
```

Transfers files over SSH.

> **SOC Note:** If SCP is initiated from the attacker's machine, the victim may only record the SSH login rather than the `scp` command itself.

---

## Detecting Tool Transfer

Useful indicators include:

### Auditd

Monitor execution of:

- wget
- curl
- scp
- ssh

---

### Authentication Logs

Review:

```bash
/var/log/auth.log
```

Look for:

- Successful SSH logins
- External IP addresses
- Unexpected authentication activity

---

### Network Traffic

Investigate downloads involving:

- Known malicious IP addresses
- Suspicious domains
- Public repositories used to host malware
- Unexpected outbound connections

---

### File Creation

Watch for newly created files in directories such as:

```text
/tmp
/var/tmp
```

Especially suspicious filenames such as:

- exploit
- shell.php
- random-looking filenames
- malware archives

---

## Dota3 Malware Case Study

The room analyzes the Dota3 Linux malware to demonstrate a complete attack chain.

Typical attack sequence:

1. Scan the Internet for exposed SSH services.
2. Perform brute-force attacks against weak passwords.
3. Gain SSH access.
4. Execute Discovery commands.
5. Download malware.
6. Establish persistence.
7. Deploy a cryptocurrency miner.

---

## Detecting Dota3

SOC analysts can detect the infection by monitoring:

### Authentication Logs

```bash
grep "Accepted" /var/log/auth.log
```

Look for:

- Successful SSH logins
- Unknown external IP addresses
- Password-based authentication

---

### Auditd

Use:

```bash
ausearch -i -x <command>
```

Search for suspicious Discovery commands such as:

- whoami
- uname
- lscpu
- wget
- curl
- nohup

---

### Process Tree Analysis

Useful commands:

```bash
ausearch -i --pid <PID>
```

Trace the parent process.

```bash
ausearch -i --ppid <PID>
```

Identify additional child processes launched by the same parent.

This helps reconstruct the complete attack chain.

---

### Additional Indicators

- Hidden files created inside `/tmp`
- Malware archives (e.g., `dota3.tar.gz`)
- Execution of `nohup`
- Internal SSH scanning activity
- Cryptominer binaries detected by EDR

---

# Key Takeaways

- Most Linux attacks begin with Discovery.
- Threat actors heavily rely on built-in Linux commands.
- Auditd provides excellent visibility into process execution.
- Authentication logs are essential for SSH investigations.
- Process tree analysis helps reconstruct attacker activity.
- Ingress Tool Transfer is commonly performed using `wget`, `curl`, or `scp`.
- Correlating multiple log sources significantly improves detection.

---

# SOC Perspective

Linux threat hunting relies heavily on correlating authentication logs, Auditd process events, file creation events, and network traffic. Rather than focusing on a single suspicious command, SOC analysts should reconstruct the complete attack chain using process tree analysis. This approach enables analysts to distinguish legitimate administrative activity from malicious behavior, identify malware deployment, and detect post-compromise actions such as credential hunting, cryptomining, or lateral movement.
