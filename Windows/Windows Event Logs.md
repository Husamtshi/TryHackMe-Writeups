# Room: Windows Event Logs

**Platform:** TryHackMe

**Path:** SOC Level 1

**Status:** Completed

---

# Objective

Learn the fundamentals of Windows Event Logs, how to investigate system activity using Event Viewer, query logs with command-line tools, and understand the importance of Event IDs during security analysis.

---

# Topics Covered

* Windows Event Logs
* Event Viewer
* Wevtutil
* Get-WinEvent
* XPath Queries
* Event IDs
* Log Filtering
* Windows Log Analysis

---

# Key Concepts

## Windows Event Logs

Windows records system activity in different log categories, including:

* System Logs
* Security Logs
* Application Logs
* Directory Service Logs
* File Replication Service Logs
* DNS Event Logs
* Custom Logs

Each log provides valuable information for troubleshooting and security investigations.

---

## Event Types

Windows logs events using different severity levels:

* Error
* Warning
* Information
* Success Audit
* Failure Audit

Understanding these event types helps analysts quickly determine the importance of an event.

---

## Event Viewer

Event Viewer is the primary graphical tool used to:

* Browse Windows logs
* Filter events
* Search for specific activity
* Investigate system and security events

---

## Wevtutil

`wevtutil` is a Windows command-line utility used to manage and query event logs.

Useful capabilities include:

* Listing available logs
* Querying events
* Exporting logs
* Displaying log information

The `/ ?` option can be used to view available commands and syntax.

---

## Get-WinEvent

`Get-WinEvent` is a PowerShell cmdlet that retrieves events from local or remote Windows systems.

It provides more flexible filtering and scripting capabilities than Event Viewer.

---

## XPath Queries

XPath allows analysts to build precise queries when searching Windows Event Logs.

Using XPath makes investigations faster by filtering only the events that match specific conditions.

---

## Event IDs

Each Windows event has a unique **Event ID**.

Knowing common Event IDs helps analysts quickly identify activities such as:

* User logons
* Failed logons
* Service creation
* Process execution
* Account changes

Understanding Event IDs is an essential skill for SOC analysts.

---

# Key Takeaways

* Windows Event Logs are one of the most important data sources during investigations.
* Event Viewer provides a graphical interface for log analysis.
* `wevtutil` allows event log management from the command line.
* `Get-WinEvent` offers powerful PowerShell-based log searching.
* XPath improves the efficiency of event filtering.
* Event IDs help analysts quickly identify security-related activities.

---

# SOC Perspective

Windows Event Logs are a primary source of evidence during incident investigations. SOC analysts use Event Viewer, PowerShell, and command-line tools to search for suspicious activity, identify attacker behavior, investigate authentication events, and reconstruct attack timelines. Understanding Event IDs and efficient log filtering is a fundamental skill for every SOC Analyst.
