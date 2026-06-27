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
