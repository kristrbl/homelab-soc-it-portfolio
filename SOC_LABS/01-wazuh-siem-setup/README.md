# Wazuh SIEM Setup & Alert Investigation

**Track:** SOC | Defensive Security | Security Monitoring
**Status:** 🔧 In Progress

---

## Scenario

Deploy a Wazuh SIEM in a home lab environment, connect Windows and Linux agents, and conduct real alert investigations. The goal is not just to install software — it is to configure meaningful detection, understand what the alerts mean, and document findings in the format a SOC analyst would use.

This lab simulates the core workflow of a tier-1 SOC analyst: monitor the dashboard, identify triggered alerts, investigate the underlying event data, determine if the alert is a true positive or false positive, and write up findings.

---

## Why This Matters for a SOC Role

SIEM tools are the primary platform for SOC work. Wazuh is open-source, widely deployed, and architecturally similar to commercial SIEMs like Splunk and Microsoft Sentinel. If you can deploy Wazuh, configure agents, understand its alert logic, and investigate alerts from raw log data, you can explain how a SIEM works in any interview — and you'll onboard faster in any SOC.

---

## Lab Environment

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE — gw-nexus |
| Wazuh Server | Dedicated Linux VM — 8 GB RAM, SOC zone (vmbr1) |
| Windows Agent | Windows 11 workstation — domain-joined to `axiom.local` |
| Windows Agent 2 | Windows 10 workstation — second domain endpoint |
| Linux Agent | Ubuntu Server — corporate zone (vmbr2) |
| Domain Controller | Windows Server 2025 — `AXIOM-POLARIS` |
| Network | SOC zone (vmbr1) monitors Corporate zone (vmbr2) |
| Wazuh Version | 4.x (current) |

---

## Tools Used

- Wazuh (server, indexer, dashboard)
- Ubuntu Server 22.04
- Windows 10 with Wazuh agent
- Wazuh web dashboard
- Windows Event Viewer (for cross-referencing)
- Linux `/var/log/auth.log` (for cross-referencing)

---

## Implementation

### Phase 1 — Deploy Wazuh Server

Use the official Wazuh all-in-one installer on Ubuntu Server 22.04:

```bash
# Download and run the Wazuh installer
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
bash wazuh-install.sh -a

# Note the admin credentials from the output
# Access dashboard: https://<server-ip>
```

After install verify all services are running:
```bash
systemctl status wazuh-manager
systemctl status wazuh-indexer
systemctl status wazuh-dashboard
```

### Phase 2 — Connect Windows Agent

On the Windows 10 VM:
1. Download the Wazuh Windows agent installer from the Wazuh dashboard
2. Run the installer — enter the Wazuh server IP when prompted
3. Start the Wazuh agent service

```powershell
# Verify agent is running
Get-Service -Name WazuhSvc
```

Verify the agent appears in the Wazuh dashboard under **Agents**.

### Phase 3 — Connect Linux Agent

On the Ubuntu Server VM:
```bash
# Add Wazuh repo and install agent
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --dearmor | tee /etc/apt/trusted.gpg.d/wazuh.gpg
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list
apt update && apt install wazuh-agent

# Configure manager IP
sed -i "s/MANAGER_IP/<your-wazuh-server-ip>/" /var/ossec/etc/ossec.conf

# Start and enable
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

### Phase 4 — Generate and Investigate Alerts

Simulate events that trigger real Wazuh rules and investigate each one.

#### Alert Investigation 1 — Failed Login Attempts (Windows)

**Trigger:** Intentionally enter wrong passwords on the Windows VM 5 times.

**In Wazuh Dashboard:**
- Navigate to **Security Events** → filter by the Windows agent
- Look for Rule ID **60122** (Multiple failed login attempts) or **18152**
- Click the alert → expand raw log → identify: source user, timestamp, workstation

**Cross-reference in Event Viewer:**
- Windows Logs → Security → Filter for Event ID **4625** (failed logon)
- Confirm the same events appear in both places

**Document:**
```
Alert: Multiple failed login attempts
Agent: CORP-WIN10
Rule ID: 60122
Event ID: 4625
Timestamps: [list them]
Account targeted: [username]
Logon type: [2 = interactive, 3 = network]
Assessment: Probable user error / testing — not a real attack in this environment
```

#### Alert Investigation 2 — Failed SSH Login (Linux)

**Trigger:** SSH to the Linux VM with a wrong password several times.

**In Wazuh Dashboard:**
- Look for Rule ID **5710** (SSH authentication failed)
- Check source IP — is it coming from inside the lab network or external?

**Cross-reference:**
```bash
grep "Failed password" /var/log/auth.log | tail -20
```

**Document:** Source IP, target user, frequency, timestamp range, assessment.

#### Alert Investigation 3 — New User Created (Windows)

**Trigger:** Create a new local user on the Windows VM.

**In Wazuh Dashboard:**
- Look for Rule ID **18152** or events related to user account changes
- Event ID **4720** (user account created)

**Document:** Who created the account, when, what account name, assess whether this is expected or suspicious in context.

---

## Alert Investigation Template

```
=== ALERT INVESTIGATION ===
Date/Time     : [timestamp]
Agent         : [hostname]
Alert Rule ID : [Wazuh rule ID]
Rule Level    : [1-15]
Description   : [what Wazuh says happened]

Raw Log Summary:
  - Event ID   : [if Windows]
  - Source     : [IP or user]
  - Target     : [account or resource]
  - Action     : [what was attempted]

Investigation Steps:
  1. [what I checked first]
  2. [what I found]
  3. [cross-reference used]

Assessment:
  - True Positive / False Positive / Benign Positive
  - Reasoning: [explain your call]

Recommended Action:
  - [close / escalate / monitor / block]

Notes:
  - [anything else relevant]
```

---

## Evidence to Collect

- [ ] Screenshot: Wazuh dashboard — all agents showing as active
- [ ] Screenshot: Security Events view with at least one triggered alert
- [ ] Screenshot: Alert detail expanded — showing raw log data
- [ ] Screenshot: Wazuh rule details for at least one investigated alert
- [ ] Screenshot: Windows Event Viewer showing same Event ID as Wazuh alert (cross-reference)
- [ ] Screenshot: Linux auth.log output matching SSH failure alert
- [ ] Completed alert investigation writeup (markdown file in this folder)

---

## Interview Talking Points

**"Have you used a SIEM before?"**
> Yes — I deployed Wazuh in my home lab. I set up the server, connected both Windows and Linux agents, and worked through several alert investigations. I'm comfortable with the dashboard, understand how rules and rule levels work, and I've cross-referenced Wazuh alerts against raw Windows Event Logs and Linux auth logs to verify what actually happened.

**"What's the difference between a true positive and a false positive?"**
> A true positive is an alert that fired correctly — the event it detected actually represents the threat or behavior the rule was written to catch. A false positive is when the rule fires on benign activity. For example, if a failed login alert fires every morning when a service account's password is slightly wrong, that's a false positive — the alert logic is right but the context makes it expected behavior. Part of SOC work is learning which alerts in your environment are reliably meaningful and which need tuning.

**"Walk me through investigating an alert."**
> I start with the alert details in the SIEM — what rule fired, what level, what it says happened. Then I look at the raw log data behind the alert to understand the actual event. I cross-reference against the source system if I can — check Event Viewer on a Windows endpoint, or auth.log on Linux. I'm looking for: who did this, from where, when, and does it match expected behavior? Then I document my findings and make a call: close it, escalate, or keep monitoring.

---

## What This Proves to an Employer

- You've deployed and configured a real SIEM — not just read about one
- You understand how agent-based monitoring works
- You can perform basic alert investigation and write it up clearly
- You know how to cross-reference SIEM data with source logs
- You understand true positive vs. false positive and can make that call
- You can produce documentation a supervisor or senior analyst could act on
