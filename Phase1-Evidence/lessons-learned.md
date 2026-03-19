# Lessons Learned — Phase 1

## What Went Well

### Network Architecture
Designed a clean four-zone network segmentation model on first attempt. The SOC/Corporate/Attack/Isolated zones map directly to enterprise security best practices and provide natural firewall boundaries.

### VM Planning
Assessed all 12 VMs and assigned clear roles before touching anything. This prevented scope creep and gave every VM a defined purpose in the lab roadmap.

### Wazuh Discovery
Found a fully installed Wazuh 4.7.5 stack (Manager + Indexer + Dashboard) already on axiom-wazuh through offline disk inspection. This saved hours of installation time and means we can jump straight to agent deployment.

## What Went Wrong

### Firewall Script Corruption
The zone firewall script (`lab-firewall.sh`) was corrupted with control characters, causing it to partially fail on boot. The FORWARD chain was set to ACCEPT with zero rules — meaning zone segmentation was completely unenforced. **All VMs could talk to all zones.**

**Root cause:** Script was likely edited with a tool that injected control characters, and the duplicate block at the end masked the issue.

**Fix:** Rewrote the script cleanly, verified rules loaded, added a drift detection cron job that checks every 5 minutes.

**Takeaway:** Never assume firewall rules are applied — always verify with `iptables -S`. Automated drift detection is essential.

### VM Network Configuration
Both axiom-wazuh and axiom-ad had no network connectivity despite being "running." axiom-wazuh was set to DHCP on a bridge with no DHCP server. axiom-ad (Windows) had no IP configured at all.

**Fix:** Mounted axiom-wazuh's disk offline and edited netplan to static IP. Windows required a different approach (startup script injection).

**Takeaway:** Always plan static IPs before deploying VMs. Document the IP scheme early.

### No Backups
Zero backups existed for any VM. A single disk failure could destroy hours of lab work.

**Fix:** Created automated weekly backup script for critical VMs (axiom-wazuh, axiom-ad, axiom-athena, athena-shield).

**Takeaway:** Backups should be the first thing configured, not the last.

## Key Decisions Made

1. **Wazuh as primary SIEM** — already installed, open source, full featured
2. **Splunk as secondary** — adds resume value, different query language to learn
3. **axiom.local as domain name** — simple, clear, no conflict with real domains
4. **SSH key-based access** — injected keys into VM disks for passwordless management
5. **Zone firewall on host** — iptables on Proxmox rather than per-VM firewalls, simpler to manage

## Metrics

| Metric | Value |
|--------|-------|
| VMs assessed | 12 |
| VMs created | 3 (109, 110, 111) |
| Security findings | 6 (1 critical, 2 high, 3 medium) |
| Findings remediated | 4 |
| Time from zero to documented architecture | ~2 hours |
