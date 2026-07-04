# Room: Blue

**Platform:** TryHackMe

**Path:** Penetration Tester

**Status:** Completed

---

# Objective

Learn how to identify a vulnerable Windows machine, discover the MS17-010 (EternalBlue) vulnerability, exploit it using Metasploit, and perform basic post-exploitation activities.

---

# Topics Covered

* Nmap Enumeration
* SMB Service
* MS17-010 (EternalBlue)
* Metasploit Framework
* Meterpreter
* Privilege Escalation
* Post-Exploitation

---

# Key Concepts

## Enumeration

Used **Nmap** to identify:

* Open ports
* SMB service
* Service version
* Potential vulnerabilities

Service enumeration helped identify a system vulnerable to **MS17-010**.

---

## SMB

SMB (Server Message Block) is a Windows protocol used for:

* File sharing
* Printer sharing
* Remote administration

Older SMB versions contain critical security vulnerabilities.

---

## MS17-010 (EternalBlue)

MS17-010 is a critical vulnerability affecting Microsoft's SMBv1 implementation.

Successful exploitation allows **Remote Code Execution (RCE)** on vulnerable Windows systems.

---

## Exploitation

Metasploit was used to exploit the vulnerability and obtain a Meterpreter session on the target machine.

---

## Post-Exploitation

After gaining access, basic post-exploitation tasks included:

* Gathering system information
* Exploring the target
* Verifying privilege level

---

# Key Takeaways

* Enumeration is essential before attempting exploitation.
* SMB is a common target in Windows environments.
* Outdated services may expose systems to critical vulnerabilities.
* Metasploit simplifies exploitation of known vulnerabilities.
* Keeping systems patched is the best defense against vulnerabilities like MS17-010.

---

# SOC Perspective

SOC analysts should monitor for unusual SMB activity, exploitation attempts targeting SMB services, unexpected remote code execution, and suspicious post-exploitation behavior. Systems running SMBv1 or missing security patches should be identified and remediated as part of vulnerability management.
