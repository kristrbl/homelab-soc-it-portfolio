# Brute Force Detection Scenario

**Track:** SOC | Defensive Security | Incident Triage
**Status:** 📋 Planned

---

## Scenario

A brute force attack is simulated against a Windows machine in the lab. The attack generates multiple failed login attempts in a short time window. Using Wazuh and Windows Event Logs, the goal is to detect the activity, investigate the alert, identify the source and target, assess severity, and produce an incident note as a tier-1 analyst would.

This lab also introduces basic MITRE ATT&CK mapping — specifically Technique T1110 (Brute Force).

---

## Why This Matters for a SOC Role

Brute force against login interfaces is one of the most common alerts a tier-1 analyst handles. Being able to recognize it in logs, understand the indicators, triage it correctly, and write it up cleanly is a core competency. This lab demonstrates that you can do all of that — and that you understand the attack from a defender's perspective.

---

## Lab Environment

| Component | Details |
|---|---|
| Wazuh Server | Ubuntu — 10.10.10.10 |
| Target (Windows) | Windows 10 VM — 10.10.20.20 |
| Attacker simulation | Hydra or manual failed login loop from Linux VM |
| Network | Isolated internal lab — no internet exposure |

---

## Tools Used

- Wazuh SIEM dashboard
- Hydra (attack simulation — lab use only, isolated network)
- Windows Event Viewer
- PowerShell
- MITRE ATT&CK Navigator (for mapping)

---

## Implementation

### Phase 1 — Simulate the Attack

From a Linux VM on the lab network, simulate repeated failed RDP or SMB login attempts against the Windows target:

```bash
# Hydra RDP brute force simulation (lab environment only)
hydra -l administrator -P /usr/share/wordlists/rockyou.txt rdp://10.10.20.20 -t 4 -w 3
```

Or manually: attempt to log in via RDP with wrong credentials 10+ times in quick succession.

### Phase 2 — Detect in Wazuh

In the Wazuh dashboard:
- Navigate to **Security Events**
- Filter by the Windows agent
- Look for alerts with Rule ID **60122** or rules mentioning "brute force" or "multiple failed logins"
- Note: Wazuh's rule levels go 1–15. Level 10+ = high severity, level 12 = brute force threshold typically

Key fields to capture from the alert:
- `data.win.system.eventID` — should be 4625
- `data.win.eventdata.targetUserName` — who was targeted
- `data.win.eventdata.ipAddress` — source IP
- `data.win.eventdata.logonType` — how login was attempted (3 = network)
- Count of events in the time window

### Phase 3 — Investigate in Event Viewer

Cross-reference on the Windows VM:
```powershell
# Export Security log events for Event ID 4625
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} |
  Select-Object TimeCreated, Message |
  Format-List |
  Out-File C:\investigation\failed-logins.txt
```

Look for:
- Same source IP across many failures
- Same target account (credential stuffing vs. username enumeration)
- Short time window (bursts = automated)

### Phase 4 — MITRE ATT&CK Mapping

| Field | Value |
|---|---|
| Tactic | Credential Access |
| Technique | T1110 — Brute Force |
| Sub-technique | T1110.001 — Password Guessing |
| Data Source | Windows Security Log, Authentication Logs |
| Detection | Multiple Event ID 4625 from same source in short window |

### Phase 5 — Write the Incident Note

```
=== INCIDENT TRIAGE NOTE ===
Date          : [date]
Analyst       : kristrbl
Severity      : Medium (lab simulation)
Status        : Investigated — Contained (lab environment)

SUMMARY
Multiple failed login attempts detected against host CORP-WIN10
from source IP 10.10.20.50. Wazuh Rule 60122 triggered after
threshold exceeded. Assessed as simulated brute force.

TIMELINE
[HH:MM] - First failed login observed (Event ID 4625)
[HH:MM] - Wazuh alert triggered (Rule 60122, Level 12)
[HH:MM] - Investigation opened
[HH:MM] - Source IP identified: 10.10.20.50
[HH:MM] - Target account confirmed: Administrator
[HH:MM] - Total failed attempts: [count] over [X] minutes
[HH:MM] - Investigation closed

FINDINGS
- Source IP: 10.10.20.50 (Linux VM — lab attacker simulation)
- Target: CORP-WIN10 — local Administrator account
- Logon Type: 3 (Network)
- Attack pattern: automated, rapid successive attempts
- Account lockout policy: [triggered / not configured]
- Password successfully guessed: No (simulation stopped)

MITRE ATT&CK
- Tactic: Credential Access
- Technique: T1110.001 — Password Guessing

ASSESSMENT
True positive. Brute force attempt against RDP. In a production
environment this would require: immediate account lockout review,
source IP block at firewall, notify system owner, check for
lateral movement from source IP.

RECOMMENDATIONS
1. Enforce account lockout policy (5 attempts = 30-minute lockout)
2. Disable RDP if not required, or restrict via firewall rule
3. Consider MFA for remote access
4. Block source IP at perimeter if external

DISPOSITION
Closed — lab simulation. No real impact.
```

---

## Evidence to Collect

- [ ] Screenshot: Hydra command running (or manual login failure sequence)
- [ ] Screenshot: Wazuh alert firing — brute force rule triggered
- [ ] Screenshot: Alert detail showing source IP, target account, event count
- [ ] Screenshot: Windows Event Viewer — 4625 events clustered in short time window
- [ ] Screenshot: Account lockout policy settings (or absence of one)
- [ ] Completed incident triage note (markdown in this folder)
- [ ] MITRE ATT&CK mapping documented

---

## Interview Talking Points

**"Describe a security alert you've investigated."**
> In my Wazuh lab I simulated a brute force attack against a Windows VM using Hydra, then investigated the resulting alerts. Wazuh triggered a brute force rule after the failed login threshold was hit. I cross-referenced the alert against Windows Event ID 4625 in Event Viewer, identified the source IP and targeted account, mapped the activity to MITRE T1110.001, and wrote up an incident note with findings and recommendations — including enabling account lockout policy and restricting RDP.

**"What is Event ID 4625 and why does it matter?"**
> Event ID 4625 is a Windows Security log event for failed logon attempts. It captures the target account, the source IP (for network logons), the logon type, and the failure reason. It matters because it's one of the primary indicators of brute force, credential stuffing, and unauthorized access attempts. A single 4625 is normal — a burst of them from the same source is an alert worth investigating.

---

## What This Proves to an Employer

- You can simulate a real attack scenario in a safe lab environment
- You can detect it in a SIEM and know which fields matter
- You can cross-reference SIEM data against raw Windows logs
- You can map findings to MITRE ATT&CK
- You can write a clear, structured incident note
- You understand defensive recommendations, not just detection
