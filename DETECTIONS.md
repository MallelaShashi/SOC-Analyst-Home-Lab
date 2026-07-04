# Active Detection Rules

## Detection Rule #1: Audit Policy Changes

**EventCode:** 4907

**Query:**
```spl
index=windows EventCode=4907 | timechart count by ComputerName
```

**What it detects:**
- Unauthorized changes to audit logging settings
- Attacker trying to disable logging
- Privilege abuse indicator

**Alert Configuration:**
- Schedule: Every 1 hour
- Trigger: Number of results > 0
- Action: Log to Splunk
- Status: ACTIVE ✅

**Real-world use:** Detects when attackers try to cover their tracks by disabling audit logs.

---

More detections coming in future phases...



## Detection Rule #2: Nmap Scan Detection (T1046)
**EventCode:** 5157
**Query:**
```spl
index=windows EventCode=5157 | stats count by Source_Address, Destination_Port | where count > 10
```
I noticed that when nmap scans the Windows host, Windows Filtering Platform logs a bunch of blocked connection attempts under EventCode 5157 instead of 5156. This rule flags any source address hitting more than 10 destination ports in a short window — that pattern usually means someone's scanning for open services before trying to exploit something.

**Alert:** Runs hourly, fires if any results come back.
**MITRE ATT&CK:** T1046 — Network Service Discovery

---

## Detection Rule #3: SMB Brute-Force Detection (T1110.001)
**EventCode:** 4624
**Query:**
```spl
index=windows EventCode=4624 Logon_Type=3 | stats count by Source_Network_Address, Account_Name | where count > 5
```
Since the Windows 10 Home VM can't run RDP, I used Hydra to brute-force SMB instead. This rule catches repeated logon attempts (Logon_Type=3, network logon) from the same source against the same account — a handful of failed/successful attempts in a row is a strong brute-force signal.

**Alert:** Runs hourly, fires if any results come back.
**MITRE ATT&CK:** T1110.001 — Brute Force: Password Guessing

---

## Detection Rule #4: Enum4linux / Anonymous Logon Detection (T1135, T1087.002)
**EventCode:** 4624
**Query:**
```spl
index=windows EventCode=4624 Logon_Type=3 Account_Name="ANONYMOUS LOGON" | stats count by Source_Network_Address
```
Enum4linux enumerates shares and accounts without needing valid credentials, which shows up as ANONYMOUS LOGON sessions in the Windows event log. This rule flags any source triggering anonymous logons — a red flag for unauthenticated recon against SMB.

**Alert:** Runs hourly, fires if any results come back.
**MITRE ATT&CK:** T1135 — Network Share Discovery, T1087.002 — Account Discovery: Domain Account
