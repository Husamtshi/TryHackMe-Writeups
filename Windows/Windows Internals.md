# Room: Windows Internals

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn the fundamental components of Windows Internals, including processes, threads, virtual memory, DLLs, the Portable Executable (PE) format, and how applications interact with the Windows kernel. Understanding these concepts helps SOC analysts investigate malware, suspicious processes, and attack techniques more effectively.

---

# Topics Covered

- Windows Processes
- Threads
- Virtual Memory
- Dynamic Link Libraries (DLLs)
- Portable Executable (PE) Format
- Windows API
- User Mode vs Kernel Mode

---

# Key Concepts

## Windows Processes

A **process** is an instance of a running program. Nearly every action performed on Windows is executed through processes.

Examples of common Windows processes include:

- `MsMpEng.exe` – Microsoft Defender
- `wininit.exe` – Windows initialization
- `lsass.exe` – Local Security Authority (credential management)

Each process contains several important components:

- Process ID (PID)
- Security Context (user privileges)
- Threads
- Virtual Address Space
- Executable Code
- Open Handles

Attackers frequently abuse legitimate processes to hide malicious activity.

Common attack techniques include:

- Process Injection (MITRE T1055)
- Process Hollowing (MITRE T1055.012)
- Process Masquerading (MITRE T1036)

---

## Process Memory Layout

Each process has its own memory structure containing:

- Executable Code
- Global Variables
- Heap
- Process Resources
- Environment Block

This layout allows Windows to efficiently manage application execution while isolating processes from one another.

---

## Threads

A **thread** is the smallest unit of execution inside a process.

Multiple threads can exist within a single process and share:

- Code
- Memory
- Resources

Each thread maintains its own:

- Stack
- Context
- Thread Local Storage (TLS)

Modern applications rely heavily on multithreading for performance.

Attackers may abuse threads through techniques such as **Thread Injection** to execute malicious code without creating new processes.

---

## Virtual Memory

Windows provides every process with its own **private virtual address space**.

Instead of accessing physical RAM directly, Windows uses a **Memory Manager** to translate virtual addresses into physical memory.

Benefits include:

- Process isolation
- Improved system stability
- Better memory management
- Reduced risk of applications corrupting each other

If physical memory becomes limited, Windows uses paging to temporarily move memory contents to disk.

---

## Dynamic Link Libraries (DLLs)

A **DLL (Dynamic Link Library)** contains reusable code and functions shared between multiple applications.

Advantages include:

- Faster application loading
- Reduced memory usage
- Code reuse
- Modular application design

Because applications depend on DLLs, attackers often target them.

Common attack techniques include:

- DLL Hijacking (MITRE T1574.001)
- DLL Side-Loading (MITRE T1574.002)
- DLL Injection (MITRE T1055.001)

Many malware families use **Run-Time Dynamic Linking** to load malicious DLLs during execution while avoiding static detection.

---

## Portable Executable (PE) Format

Windows executable files use the **Portable Executable (PE)** format.

Common PE file types include:

- `.exe`
- `.dll`
- `.sys`

Every executable begins with the **DOS Header**, identified by the hexadecimal signature:

```text
4D 5A (MZ)
```

Important PE sections include:

| Section | Purpose |
|----------|---------|
| `.text` | Executable code |
| `.data` | Initialized variables |
| `.rdata` / `.idata` | Imports and DLL references |
| `.reloc` | Relocation information |
| `.rsrc` | Resources (icons, images, dialogs) |
| `.debug` | Debugging information |

The PE format is one of the first artifacts examined during malware static analysis.

---

## Windows API

Applications interact with Windows through the **Windows API (Win32 API)**.

Instead of communicating directly with hardware, applications use API calls to request operating system services such as:

- File operations
- Memory allocation
- Process creation
- Network communication

Many malware families abuse Windows API calls to inject code, allocate memory, and manipulate other processes.

---

## User Mode vs Kernel Mode

Windows separates execution into two protection levels.

### User Mode

- Limited privileges
- No direct hardware access
- Can only access its own virtual memory
- Standard applications run here

### Kernel Mode

- Full system privileges
- Direct hardware access
- Access to all physical memory
- Used by the Windows kernel and device drivers

Applications switch from **User Mode** to **Kernel Mode** through **System Calls** and Windows API functions whenever privileged operations are required.

---

# Key Takeaways

- Processes are the foundation of Windows execution.
- Threads allow multiple execution paths within a single process.
- Virtual Memory isolates applications and improves system stability.
- DLLs enable code reuse but are common malware targets.
- Windows executables follow the Portable Executable (PE) format.
- The **MZ (4D 5A)** header identifies PE files.
- Windows API provides controlled interaction with the operating system.
- Windows separates execution into **User Mode** and **Kernel Mode** for security and stability.

---

# SOC Perspective

Understanding Windows Internals helps SOC analysts investigate malicious activity beyond simple log analysis. Many attack techniques—including Process Injection, DLL Hijacking, Process Hollowing, and Memory Injection—directly target Windows internals. Familiarity with processes, threads, PE files, DLLs, and Windows API behavior enables analysts to recognize abnormal process execution, identify malware techniques, and better interpret Sysmon events, EDR telemetry, and incident investigations.
