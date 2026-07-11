# Room: Windows Threat Detection 3

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn how attackers establish Command and Control (C2), maintain persistence on compromised Windows systems, and how SOC analysts detect these techniques using Windows Security Logs and Sysmon.

---

# Topics Covered

* Command and Control (C2)
* Persistence
* User Account Persistence
* Windows Services
* Scheduled Tasks
* Startup Folder
* Run Registry Keys
* Sysmon Detection
* Windows Security Logs

---

# Key Concepts

## Command and Control (C2)

After gaining initial access, attackers establish a Command and Control (C2) channel to remotely control the compromised system.

C2 allows attackers to:

* Execute commands remotely
* Download additional malware
* Maintain long-term access
* Continue the attack without requiring another phishing email or RDP session

Many modern attacks use lightweight malware that later downloads a full-featured RAT or C2 agent.

---

## Persistence

Persistence enables attackers to maintain access even after:

* System reboots
* User logoff
* Password changes

Persistence is one of the most important stages following Initial Access.

---

## Backdoored User Accounts

Attackers may create hidden local users and add them to privileged groups.

Relevant Windows Security Event IDs include:

* **4720** – User Created
* **4732** – User Added to a Security Group
* **4724** – Password Reset

SOC analysts should investigate:

* Who created the account?
* Was the action authorized?
* What other events occurred within the same Logon ID?

---

## Windows Services

Attackers often create malicious Windows Services that automatically start after reboot.

Detection methods include:

* Sysmon Event ID **1** (execution of `sc.exe`)
* Security Event ID **4697**
* System Event ID **7045**
* Suspicious child processes launched by **services.exe**

---

## Scheduled Tasks

Scheduled Tasks are one of the most common persistence techniques.

Attackers use them to automatically execute malware after startup or at scheduled times.

Detection methods include:

* Sysmon Event ID **1** (`schtasks.exe`)
* Security Event ID **4698**
* Suspicious processes launched by **svchost.exe** (Schedule service)

---

## Startup Folder

Programs placed inside the Windows Startup folder automatically execute when the user logs in.

Common locations include:

* User Startup Folder
* All Users Startup Folder

Detection relies on:

* Sysmon Event ID **11** (File Creation)

Because this folder is normally almost empty, newly created executables should be investigated.

---

## Run Registry Keys

Another common persistence mechanism is adding malicious entries to:

* HKCU\Software\Microsoft\Windows\CurrentVersion\Run
* HKLM\Software\Microsoft\Windows\CurrentVersion\Run

Detection relies on:

* Sysmon Event ID **13** (Registry Value Set)

Unexpected registry modifications may indicate malware persistence.

---

# Key Takeaways

* C2 allows attackers to remotely manage compromised systems.
* Persistence enables attackers to survive reboots and maintain long-term access.
* Backdoor accounts are commonly used for persistent access.
* Windows Services and Scheduled Tasks are frequently abused by attackers.
* Startup folders and Run Registry Keys are common user-level persistence techniques.
* Correlating Security Logs with Sysmon provides better visibility during investigations.

---

# SOC Perspective

SOC analysts should continuously monitor for persistence techniques by correlating Windows Security Logs and Sysmon events. User account creation, privilege changes, scheduled tasks, Windows services, startup folder modifications, registry Run keys, and suspicious parent-child process relationships are all valuable indicators of compromise. Early detection of persistence mechanisms can prevent attackers from maintaining long-term access to enterprise environments.
