# Security Audit — axiom-core Infrastructure

**Date:** 2026-03-18
**Auditor:** ATHENA (automated assessment)
**Scope:** Proxmox host (axiom-core), network segmentation, VM exposure

---

## Executive Summary

The lab infrastructure had **one critical finding** (firewall rules not loaded, zone segmentation unenforced) which was remediated during this audit. Several medium-severity issues remain for hardening.

---

## Findings

### CRITICAL — Zone Firewall Was Not Loaded

| Item | Detail |
|------|--------|
| Severity | **CRITICAL** |
| Status | **REMEDIATED** |
| Description | Firewall script `/usr/local/bin/lab-firewall.sh` runs via `@reboot` crontab, but iptables showed `FORWARD ACCEPT` with zero rules. Script contained control characters causing silent partial failure. |
| Impact | All network zones (SOC, Corporate, Attack) could communicate freely — no segmentation enforced |
| Fix Applied | Cleaned script of corruption, re-applied rules, verified FORWARD policy is DROP with correct zone rules |

**Verified rules after fix:**
```
-P FORWARD DROP
-A FORWARD -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -s 10.10.10.0/24 -j ACCEPT                     # SOC -> ALL
-A FORWARD -s 10.10.20.0/24 -d 10.10.10.0/24 -j ACCEPT    # Corp -> SOC only
-A FORWARD -s 10.10.30.0/24 -d 10.10.20.0/24 -j ACCEPT    # Attack -> Corp only
-A FORWARD -s 10.10.30.0/24 -d 10.10.10.0/24 -j DROP       # Attack -> SOC BLOCKED
-A FORWARD -s 10.10.20.0/24 -d 10.10.30.0/24 -j DROP       # Corp -> Attack BLOCKED
```

### HIGH — Proxmox Firewall Disabled

| Item | Detail |
|------|--------|
| Severity | HIGH |
| Status | Open |
| Description | `pve-firewall status` shows `disabled/running`. The built-in PVE firewall is not protecting the management interface (port 8006). |
| Recommendation | Enable PVE firewall with rules allowing only trusted IPs to access port 8006, SSH (22) |

### HIGH — SSH Root Login Enabled (Password)

| Item | Detail |
|------|--------|
| Severity | HIGH |
| Status | Open |
| Description | `PermitRootLogin yes` in sshd_config. Password authentication appears enabled by default. |
| Recommendation | Set `PermitRootLogin prohibit-password` (key-only). Explicitly set `PasswordAuthentication no`. |

### MEDIUM — No VM Backups

| Item | Detail |
|------|--------|
| Severity | MEDIUM |
| Status | Open |
| Description | `/var/lib/vz/dump/` is empty. No backup schedule configured. |
| Recommendation | Configure vzdump scheduled backups for critical VMs (at minimum: axiom-wazuh, axiom-ad after Lab 1) |

### MEDIUM — rpcbind Exposed

| Item | Detail |
|------|--------|
| Severity | MEDIUM |
| Status | Open |
| Description | Port 111 (rpcbind) listening on all interfaces. Not needed for Proxmox operation. |
| Recommendation | `systemctl disable --now rpcbind rpcbind.socket` |

### MEDIUM — Pending System Updates

| Item | Detail |
|------|--------|
| Severity | MEDIUM |
| Status | Open |
| Description | Multiple packages have pending security updates (base-files, bash, dpkg, e2fsprogs, chrony, gpg). |
| Recommendation | Schedule maintenance window: `apt update && apt upgrade -y` |

### LOW — Proxmox Firewall Script Authorship

| Item | Detail |
|------|--------|
| Severity | LOW |
| Status | Open |
| Description | Script header has placeholder `[Your Name]` and `[Today]` — should be updated for portfolio documentation |
| Fix | Updated in cleaned script |

---

## Positive Findings

| Item | Status |
|------|--------|
| fail2ban | Active and running |
| IP forwarding | Correctly enabled only where needed |
| Zone architecture | Well-designed 4-zone model (SOC, Corp, Attack, Isolated) |
| NAT masquerade | Correctly scoped per-zone outbound |
| Key-based SSH | Working (axiom-athena -> axiom-core) |
| Attack->SOC logging | Blocked traffic is logged with prefix for SIEM ingestion |

---

## Remediation Priority

1. **Done** — Firewall rules reapplied and script fixed
2. **Next** — Harden SSH config
3. **Next** — Disable rpcbind
4. **Next** — Apply system updates
5. **Later** — Enable PVE firewall
6. **Later** — Configure automated backups
