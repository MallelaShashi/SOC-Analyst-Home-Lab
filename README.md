\# SOC Analyst Home Lab



A hands-on detection engineering lab built to simulate real attacker behavior and catch it using Splunk.



\## Overview

This lab runs a Windows 10 VM (Splunk Enterprise + Sysmon) alongside a Kali Linux attacker VM on VMware. I used Kali to run real attack techniques — network scanning, brute-force, and enumeration — against the Windows host, then built and tuned Splunk detections to catch each one using native Windows event logs and Sysmon telemetry.



\## Architecture

\- \*\*Windows 10 VM\*\* — Splunk Enterprise, Sysmon, Universal Forwarder

\- \*\*Kali Linux VM\*\* — attacker box (nmap, Hydra, enum4linux)

\- Both VMs on the same VMware NAT/host-only network



\## Attacks Simulated \& Detections Built

| Technique | Tool | MITRE ATT\&CK | EventCode |

|---|---|---|---|

| Network Service Discovery | nmap | T1046 | 5157 |

| SMB Brute-Force | Hydra | T1110.001 | 4624 |

| Anonymous Enumeration | enum4linux | T1135, T1087.002 | 4624 |

| Audit Log Tampering | manual | — | 4907 |



Full detection logic, SPL queries, and alert configs: \[DETECTIONS.md](DETECTIONS.md)



\## Setup

Step-by-step build instructions (Splunk, Sysmon, Universal Forwarder, inputs.conf): \[SETUP\_GUIDE.md](SETUP\_GUIDE.md)



\## Screenshots

Evidence of attacks, detections, and Splunk dashboards: \[screenshots/](screenshots/)



\## Key Findings

\- Windows 10 Home can't run RDP — pivoted the brute-force target to SMB instead

\- Blocked nmap traffic logs under EventCode 5157, not 5156

\- Anonymous SMB enumeration shows up as EventCode 4624 with Logon\_Type=3 and ANONYMOUS LOGON

