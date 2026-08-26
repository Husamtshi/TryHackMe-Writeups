# Room: Volatility

**Platform:** TryHackMe  
**Path:** SOC Level 1  
**Status:** Completed

---

# Objective

Learn the fundamentals of **memory forensics** using Volatility and understand how memory dumps can be investigated for processes, network connections, DLLs, drivers, and advanced malware evasion techniques.

---

# Memory Acquisition

Memory dumps can be collected from physical machines using tools such as:

- FTK Imager
- Redline
- DumpIt
- Win32dd / Win64dd
- Memoryze
- FastDump

For virtual machines, memory may also be stored in files depending on the hypervisor:

| Hypervisor | Memory File |
|---|---|
| VMware | `.vmem` |
| Hyper-V | `.bin` |
| Parallels | `.mem` |
| VirtualBox | `.sav` |

Memory should be handled carefully because it contains volatile information that can change or be lost.

---

# Identifying the Operating System

In **Volatility 2**, `imageinfo` can suggest possible Windows profiles:

```bash
python2 vol.py -f memory.dmp imageinfo
```

`imageinfo` is not always accurate, so multiple profiles may need to be tested.

In **Volatility 3**, OS profiles are no longer required.

```bash
python3 vol.py -f memory.dmp windows.info
```

Other operating systems:

```bash
python3 vol.py -f memory.dmp linux.info
python3 vol.py -f memory.dmp mac.info
```

---

# Process Analysis

Processes are one of the first things to investigate in a memory dump.

## pslist

Displays processes using the normal Windows process list.

```bash
python3 vol.py -f memory.dmp windows.pslist
```

Useful for identifying:

- Running processes
- Terminated processes
- Process IDs
- Exit times

Rootkits may attempt to unlink themselves from the normal process list.

---

## psscan

Searches memory for `_EPROCESS` structures rather than relying only on the normal process list.

```bash
python3 vol.py -f memory.dmp windows.psscan
```

This can reveal hidden or unlinked processes.

It can also produce false positives, so results should be validated.

A useful comparison is:

```text
pslist
   +
psscan
```

If a process appears in `psscan` but not `pslist`, it deserves further investigation.

---

## pstree

Displays processes according to their parent-child relationships.

```bash
python3 vol.py -f memory.dmp windows.pstree
```

Example:

```text
explorer.exe
      ↓
powershell.exe
      ↓
cmd.exe
      ↓
suspicious.exe
```

Unexpected parent-child relationships can indicate malicious execution.

---

# Network Analysis

## netstat

Volatility can investigate network connections that existed when the memory image was captured.

```bash
python3 vol.py -f memory.dmp windows.netstat
```

Useful information includes:

- Local IP
- Remote IP
- Ports
- Connection state
- Associated processes

Suspicious connections may indicate:

- Command and Control
- Malware communication
- Data exfiltration
- Remote access

Volatility network analysis can sometimes be unstable, especially with older Windows versions. Other tools such as `bulk_extractor` may help with deeper network extraction.

---

# DLL Analysis

## dlllist

Displays DLLs loaded by processes.

```bash
python3 vol.py -f memory.dmp windows.dlllist
```

Useful for identifying:

- Suspicious DLLs
- Malware-related modules
- Unexpected libraries
- DLLs associated with suspicious processes

---

# Advanced Memory Forensics

Advanced memory analysis focuses on malware that attempts to hide itself or manipulate Windows internals.

Common techniques include:

- Rootkits
- Kernel manipulation
- Hooking
- Malicious drivers
- Hidden modules

---

# SSDT Hooking

The **System Service Descriptor Table (SSDT)** contains pointers used by Windows to access system services.

Malware can modify these pointers to redirect execution to attacker-controlled code.

```bash
python3 vol.py -f memory.dmp windows.ssdt
```

The `ssdt` plugin can identify potential SSDT hooks.

Not every hook is automatically malicious. Legitimate software can also use hooking techniques, so results should be compared against a known baseline.

---

# Kernel Modules

## modules

Lists loaded kernel modules.

```bash
python3 vol.py -f memory.dmp windows.modules
```

Useful for identifying:

- Loaded drivers
- Kernel modules
- Potential malicious drivers

Hidden or inactive drivers may not appear.

---

## driverscan

Searches memory for driver objects.

```bash
python3 vol.py -f memory.dmp windows.driverscan
```

This can reveal drivers that may not appear in the normal module list.

A useful approach is:

```text
modules
   ↓
Investigate suspicious modules
   ↓
driverscan
   ↓
Look for hidden drivers
```

---

# Other Useful Plugins

Additional plugins can assist with advanced malware investigations:

```text
modscan
driverirp
callbacks
idt
apihooks
moddump
handles
```

Some plugins are specific to Volatility 2 or require third-party/custom plugins.

---

# SOC Investigation Workflow

A practical memory investigation can follow this order:

```text
Memory Dump
     ↓
Identify OS
     ↓
List Processes
     ↓
Compare pslist / psscan
     ↓
Investigate Process Tree
     ↓
Check Network Connections
     ↓
Investigate DLLs
     ↓
Investigate Drivers
     ↓
Check Advanced Evasion
     ↓
Build Attack Timeline
```

The goal is not to run every plugin blindly.

Start with basic enumeration, identify suspicious indicators, and then use more advanced plugins to investigate those indicators.

---

# Key Takeaways

- Memory dumps contain valuable volatile evidence.
- Volatility is a powerful framework for memory forensics.
- `pslist` shows processes from the normal process list.
- `psscan` can help identify hidden or unlinked processes.
- `pstree` helps understand parent-child process relationships.
- `netstat` can reveal network connections from memory.
- `dlllist` helps investigate loaded DLLs.
- `ssdt` can identify potential SSDT hooks.
- `modules` lists loaded kernel modules.
- `driverscan` can help discover hidden drivers.
- Advanced plugins are useful when investigating rootkits and sophisticated malware.
- Analysts should correlate multiple plugins instead of relying on a single result.

---

# SOC Analyst Perspective

The most important lesson from Volatility is that **memory can reveal activity that may not be visible through normal endpoint logs**.

A suspicious process, hidden driver, unusual network connection, or injected DLL can provide valuable evidence during an investigation.

The best approach is:

```text
Find the anomaly
      ↓
Validate it
      ↓
Correlate it with other memory artifacts
      ↓
Determine whether it is malicious
      ↓
Build the attack timeline
```
