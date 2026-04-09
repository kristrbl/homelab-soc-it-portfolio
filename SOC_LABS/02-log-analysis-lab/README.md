# Log Analysis Lab — Splunk

**Track:** SOC | Defensive Security | Log Analysis
**Status:** 🔧 In Progress

---

## Scenario

A dedicated Splunk instance is deployed in the SOC zone of the homelab. Windows and Linux endpoints forward logs to Splunk via Universal Forwarder. The goal is to search, filter, and analyze log data to identify patterns, investigate events, and build basic detection queries using Splunk's Search Processing Language (SPL).

This lab covers the core Splunk skills a tier-1 SOC analyst uses daily: searching logs, building queries, understanding sourcetypes, and investigating specific events.

---

## Why This Matters for a SOC Role

Splunk is the most widely deployed SIEM in enterprise environments. Knowing how to write SPL queries, navigate the Splunk interface, and investigate events in Splunk is one of the most marketable SOC skills available. Combined with Wazuh experience, this lab shows you can work in both open-source and commercial SIEM environments.

---

## Lab Environment

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE — gw-nexus |
| Splunk Server | Dedicated Linux VM — 8 GB RAM, SOC zone (vmbr1) |
| Windows Forwarder | Windows 11 workstation — Universal Forwarder installed |
| Windows Forwarder 2 | Windows 10 workstation — Universal Forwarder installed |
| Linux Forwarder | Ubuntu Server — Universal Forwarder installed |
| Domain Controller | Windows Server 2025 — `AXIOM-POLARIS`, logs forwarded |
| Network | SOC zone (vmbr1) receives logs from Corporate zone (vmbr2) |

---

## Tools Used

- Splunk Enterprise (free license — up to 500 MB/day)
- Splunk Universal Forwarder (Windows + Linux)
- Splunk Search Processing Language (SPL)
- Windows Event Logs (Security, System, Application)
- Linux `/var/log/auth.log`, `/var/log/syslog`

---

## Implementation

### Phase 1 — Install Splunk and Configure Receiving

```bash
# On Splunk server — enable receiving on port 9997
/opt/splunk/bin/splunk enable listen 9997 -auth admin:password

# Verify receiver is active
/opt/splunk/bin/splunk list forward-server
```

### Phase 2 — Install Universal Forwarder on Windows Endpoints

Install the Splunk Universal Forwarder on each Windows VM. Configure it to send to the Splunk server:

```
# inputs.conf — on the forwarder
[WinEventLog://Security]
disabled = 0
index = windows

[WinEventLog://System]
disabled = 0
index = windows

[WinEventLog://Application]
disabled = 0
index = windows
```

### Phase 3 — Install Universal Forwarder on Linux

```bash
# Install forwarder and point to Splunk server
/opt/splunkforwarder/bin/splunk add forward-server <splunk-ip>:9997
/opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log -index linux
/opt/splunkforwarder/bin/splunk add monitor /var/log/syslog -index linux
```

---

## SPL Queries — Investigation Examples

### Find all failed logins (Windows)

```splunk
index=windows EventCode=4625
| stats count by Account_Name, src_ip, Logon_Type
| sort -count
```

### Find successful logins outside business hours

```splunk
index=windows EventCode=4624
| eval hour=strftime(_time, "%H")
| where hour < 7 OR hour > 18
| table _time, Account_Name, src_ip, Logon_Type
```

### Count failed SSH attempts by source IP (Linux)

```splunk
index=linux source="/var/log/auth.log" "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by src_ip
| sort -count
```

### Find new user account creation (Windows)

```splunk
index=windows EventCode=4720
| table _time, Account_Name, Subject_Account_Name, ComputerName
```

### Detect potential brute force (10+ failures in 5 minutes)

```splunk
index=windows EventCode=4625
| bucket _time span=5m
| stats count by _time, Account_Name, src_ip
| where count >= 10
```

---

## Evidence to Collect

- [ ] Screenshot: Splunk dashboard — data ingestion confirmed from all agents
- [ ] Screenshot: SPL search returning Windows Event ID 4625 results
- [ ] Screenshot: Failed login query with count by source IP
- [ ] Screenshot: After-hours login search results
- [ ] Screenshot: Brute force detection query results
- [ ] Screenshot: Linux auth.log events visible in Splunk

---

## Interview Talking Points

**"Have you used Splunk?"**
> Yes — I have a Splunk instance running in my homelab SOC zone with Universal Forwarders on Windows and Linux endpoints sending logs in real time. I've written SPL queries to search Windows Security logs for failed logins, detect brute force patterns, find after-hours authentication, and track new user creation. I'm comfortable with the Splunk interface, understand how indexes and sourcetypes work, and can build basic detection queries from scratch.

**"Write me a Splunk query to find failed logins."**
> `index=windows EventCode=4625 | stats count by Account_Name, src_ip | sort -count` — that gives me failed login attempts grouped by account and source IP, sorted by frequency. From there I can threshold it: `| where count > 10` to flag potential brute force.

---

## What This Proves to an Employer

- You've deployed and configured Splunk in a real environment — not just a demo
- You can write SPL queries from scratch to answer specific investigative questions
- You understand how log forwarding works (Universal Forwarder → indexer)
- You can correlate Windows and Linux logs in a single SIEM
- You have experience with both Wazuh (open source) and Splunk (industry standard)
