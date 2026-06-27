# Setup Guide - Step by Step

## What You'll Need
- VMware Workstation
- Windows 10 VM (6 GB RAM)
- Kali Linux VM (4 GB RAM)
- 40 GB free disk space

## Phase 1: Install Splunk Enterprise

1. Download from: https://www.splunk.com/download/splunk-enterprise
2. Run installer (Windows 64-bit)
3. Accept license, create user: `splunk-admin`
4. Set strong password
5. Check "Start on system startup"
6. Access at: https://localhost:8000
7. Default login: admin / changeme
8. Create indexes: `windows`, `sysmon`

## Phase 2: Install Sysmon

1. Create folder: `C:\Sysmon`
2. Download Sysmon: https://download.sysinternals.com/files/Sysmon.zip
3. Extract to `C:\Sysmon`
4. Download config: https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml
5. Run: `C:\Sysmon\sysmon64.exe -accepteula -i sysmonconfig-export.xml`

## Phase 3: Install Universal Forwarder

1. Download from: https://www.splunk.com/download/universal-forwarder
2. Install (Windows 64-bit)
3. Set receiving indexer: 127.0.0.1:9997
4. Create inputs.conf at: `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`

Inputs.conf content:

[WinEventLog://Security]

disabled = false

index = windows

sourcetype = WinEventLog:Security
[WinEventLog://System]

disabled = false

index = windows

sourcetype = WinEventLog:System
[WinEventLog://Microsoft-Windows-Sysmon/Operational]

disabled = false

index = sysmon

sourcetype = WinEventLog:Sysmon/Operational

5. Restart forwarder: `Restart-Service SplunkForwarder`

## Phase 4: Create Detection

1. In Splunk, go to: Search & Reporting
2. Search: `index=windows EventCode=4907`
3. Save as Alert
4. Schedule: Every 1 hour
5. Trigger: Number of results > 0
6. Action: Log event
7. Save

## Verification

Check in Splunk:
- Search: `index=windows OR index=sysmon`
- Should show 30,000+ events
- Alerts section shows your detection active

Done! ✅
