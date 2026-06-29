# Room: Ice

**Platform:** TryHackMe

**Path:** Penetration Tester

**Status:** Completed

---

# Objective

The objective of this room was to perform a complete penetration testing workflow, including reconnaissance, vulnerability identification, exploitation, privilege escalation, and post-exploitation using Metasploit.

---

# Topics Covered

* Nmap Enumeration
* Icecast Service
* Vulnerability Identification
* Metasploit Framework
* Meterpreter
* Privilege Escalation
* Password Extraction
* Kiwi Extension

---

# Reconnaissance

The first step was scanning the target using Nmap.

The scan identified:

* Open ports
* Running services
* Service versions

Service version detection helped identify a vulnerable version of Icecast.

---

# Icecast Vulnerability

The vulnerable Icecast service contained a known security vulnerability that allows Remote Code Execution (RCE).

An attacker can exploit this vulnerability to execute arbitrary code on the target system without requiring valid credentials.

---

# Exploitation

The vulnerability was exploited using the Metasploit Framework.

After selecting the appropriate exploit and configuring the required options, a Meterpreter session was successfully established on the target.

---

# Meterpreter

Meterpreter provides an interactive shell that allows post-exploitation activities such as:

* File system navigation
* Process interaction
* Command execution
* Privilege management
* Module loading

---

# Privilege Escalation

After gaining initial access, privilege escalation techniques were used to obtain higher privileges on the target system.

Higher privileges allow broader access to system resources and administrative functionality.

---

# Password Extraction

The room demonstrated how credentials can be extracted from memory after obtaining sufficient privileges.

These credentials may be useful for lateral movement or further investigation during a penetration test.

---

# Kiwi Extension

The Kiwi extension is a Meterpreter module based on Mimikatz.

Common capabilities include:

* Dumping credentials
* Extracting password hashes
* Retrieving cached credentials
* Accessing Kerberos tickets

Kiwi is commonly used during post-exploitation after administrative privileges have been obtained.

---

# Key Takeaways

* Nmap is used to identify services and versions running on a target.
* Service enumeration helps discover known vulnerabilities.
* Metasploit simplifies exploitation using existing exploit modules.
* Meterpreter provides a powerful post-exploitation environment.
* Privilege escalation is often required after initial access.
* Credential dumping demonstrates the importance of protecting credentials stored in memory.

---

# SOC Perspective

From a defensive perspective, SOC analysts should monitor for:

* Exploitation attempts targeting vulnerable services.
* Unusual process creation.
* Privilege escalation events.
* Credential dumping activity.
* Suspicious use of administrative tools.
* Unexpected Meterpreter-like behavior or command execution.

Early detection of these activities can significantly reduce the impact of a successful compromise.

---

# Personal Notes

Topics to review later:

* Meterpreter Commands
* Windows Privilege Escalation
* Credential Dumping Detection
* Mimikatz & Kiwi
* Metasploit Modules
* Exploit Mitigations
