# Room: Core Windows Processes

**Platform:** TryHackMe  
**Path:** SOC Level 1  
**Status:** Completed

---

# Objective

Understand the core processes that normally run on Windows and learn how to recognise abnormal process behaviour.

For a SOC analyst, knowing normal Windows process behaviour is important because malware can disguise itself as legitimate system processes. By checking the process name, path, parent process, user, and number of instances, we can identify suspicious activity.

---

# Task Manager

Windows Task Manager provides visibility into running processes and their resource usage.

Important information when investigating a process includes:

- Process name
- PID
- Parent process
- Image path
- Command line
- User account
- Number of instances

A process name alone is not enough to determine whether it is legitimate.

For example:

```text
svchost.exe
```

can be legitimate, but a copy running from an unusual directory could be suspicious.

---

# Core Windows Process Tree

A useful process relationship to remember is:

```text
System
   ↓
smss.exe
   ↓
csrss.exe
   ↓
wininit.exe
   ↓
services.exe
   ↓
svchost.exe
```

Other important processes include:

```text
wininit.exe
   └── lsass.exe

smss.exe
   └── winlogon.exe

userinit.exe
   └── explorer.exe
```

The exact parent-child relationships are extremely useful when investigating suspicious processes.

---

# 1. System

The **System** process represents the Windows kernel and system-level activity.

Important point:

```text
System PID = 4
```

A different PID for the System process would be highly abnormal.

---

# 2. smss.exe

`smss.exe` stands for **Session Manager Subsystem**.

It is responsible for creating Windows sessions and starting important processes during system startup.

Normal location:

```text
C:\Windows\System32\smss.exe
```

Important behaviour:

- Runs as SYSTEM.
- Starts very early during boot.
- Creates processes such as `csrss.exe`.
- Starts `winlogon.exe` for the user session.
- Some child instances terminate after completing their startup tasks.

Suspicious indicators:

- Incorrect file path
- Misspelled process name
- Unexpected parent process
- Running under a normal user account

---

# 3. csrss.exe

`csrss.exe` stands for **Client Server Runtime Process**.

It is a critical Windows subsystem process responsible for functions including:

- Win32 subsystem operations
- Console windows
- Process and thread management

Normal location:

```text
C:\Windows\System32\csrss.exe
```

Normally there can be multiple instances because different Windows sessions can have their own instance.

Suspicious indicators:

- Running outside `System32`
- Misspelled name such as `cssrs.exe`
- Unexpected parent process
- Running under an unusual account

---

# 4. wininit.exe

`wininit.exe` stands for **Windows Initialization Process**.

It starts important system processes in Session 0, including:

```text
wininit.exe
   ├── services.exe
   ├── lsass.exe
   └── lsaiso.exe
```

`lsaiso.exe` may not be present when Credential Guard is not enabled.

Normal behaviour:

- One main instance
- Runs as SYSTEM
- Starts shortly after boot
- Located in `C:\Windows\System32`

Suspicious indicators:

- Multiple unexpected instances
- Incorrect path
- Misspelled filename
- Running under a non-SYSTEM account

---

# 5. services.exe

`services.exe` is the **Service Control Manager (SCM)**.

It manages Windows services, including:

- Starting services
- Stopping services
- Loading services
- Managing auto-start services

Normal relationship:

```text
wininit.exe
      ↓
services.exe
```

Normally:

```text
services.exe = 1 instance
```

Suspicious indicators:

- Multiple instances
- Incorrect parent process
- Running outside `C:\Windows\System32`
- Running under an unexpected account

---

# 6. svchost.exe

`svchost.exe` is the **Service Host** process.

It hosts Windows services implemented as DLLs.

Normal relationship:

```text
services.exe
      ↓
svchost.exe
```

Windows can have many `svchost.exe` instances.

A useful indicator when investigating it is the:

```text
-k
```

parameter.

Example:

```text
svchost.exe -k netsvcs
```

Suspicious indicators:

- Incorrect image path
- Misspelled name such as `scvhost.exe`
- Unexpected parent process
- Missing or unusual command-line parameters

---

# 7. lsass.exe

`lsass.exe` stands for **Local Security Authority Subsystem Service**.

It is responsible for important security functions such as:

- Authentication
- Password changes
- Access tokens
- Security policy enforcement

Normal relationship:

```text
wininit.exe
      ↓
lsass.exe
```

Normal location:

```text
C:\Windows\System32\lsass.exe
```

Because LSASS handles credentials, it is a high-value process for attackers.

Suspicious indicators include:

- LSASS running from another directory
- Unexpected parent process
- Multiple abnormal instances
- Suspicious processes accessing LSASS memory

---

# 8. winlogon.exe

`winlogon.exe` manages the Windows login process.

It handles functions such as:

- Secure Attention Sequence (Ctrl + Alt + Delete)
- User authentication
- Loading the user profile
- Locking the workstation
- Starting the user shell

Normal location:

```text
C:\Windows\System32\winlogon.exe
```

Its startup relationship is associated with:

```text
smss.exe
   ↓
winlogon.exe
```

The parent may not appear in process-monitoring tools because `smss.exe` terminates after creating the required session processes.

Suspicious indicators:

- Incorrect path
- Misspelled filename
- Unexpected visible parent process
- Running under an unusual account

---

# 9. explorer.exe

`explorer.exe` is the Windows user shell.

It provides the graphical interface users interact with, including:

- Desktop
- Taskbar
- File Explorer
- Start menu

The normal startup relationship involves:

```text
userinit.exe
      ↓
explorer.exe
```

However, `userinit.exe` normally terminates after launching the user's shell, so the parent may no longer appear in process-monitoring tools.

Suspicious indicators:

- Explorer running from an unusual directory
- Unexpected command-line arguments
- Suspicious child processes
- Abnormal network activity

---

# Process Investigation Methodology

When a SOC alert reports a suspicious Windows process, don't investigate only the filename.

Check:

```text
Process Name
      ↓
Image Path
      ↓
Parent Process
      ↓
Command Line
      ↓
User
      ↓
PID
      ↓
Network Connections
      ↓
Child Processes
```

For example:

```text
svchost.exe
```

by itself is not suspicious.

But:

```text
C:\Users\Public\svchost.exe
```

with:

```text
Parent = powershell.exe
```

would be highly suspicious.

---

# Common Masquerading Techniques

Attackers often create malicious processes with names similar to legitimate Windows processes.

Examples:

```text
svchost.exe   ← legitimate
scvhost.exe   ← suspicious

csrss.exe     ← legitimate
cssrs.exe     ← suspicious
```

Therefore, analysts should always verify:

- Exact filename
- File path
- Parent process
- User
- Command line
- Digital signature

---

# SOC Analyst Perspective

The main lesson from this room is that **process context matters more than the process name itself**.

A legitimate Windows process can become suspicious when it:

- Runs from the wrong directory
- Has the wrong parent
- Runs under the wrong account
- Uses unusual command-line arguments
- Creates suspicious child processes
- Makes unexpected network connections

Understanding normal Windows process behaviour allows SOC analysts to recognise deviations that may indicate malware, persistence, or process masquerading.

---

# Key Takeaways

- The Windows **System** process should normally have PID `4`.
- `smss.exe` is responsible for session creation and early startup.
- `csrss.exe` is a critical Windows subsystem process.
- `wininit.exe` starts important system processes.
- `services.exe` manages Windows services and normally has one main instance.
- `svchost.exe` hosts Windows services and commonly uses the `-k` parameter.
- `lsass.exe` handles important authentication and security functions.
- `winlogon.exe` manages Windows logon functionality.
- `explorer.exe` provides the Windows graphical shell.
- Process name alone is not enough to determine whether a process is malicious.
- Parent-child relationships, image paths, command lines, users, and behaviour are essential during endpoint investigations.

---

# SOC Investigation Cheat Sheet

```text
Suspicious Process
       ↓
Check Name
       ↓
Check Image Path
       ↓
Check Parent
       ↓
Check Command Line
       ↓
Check User
       ↓
Check Children
       ↓
Check Network Connections
       ↓
Determine:
Legitimate / Suspicious / Malicious
```
