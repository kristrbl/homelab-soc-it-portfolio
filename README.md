# Enterprise Home Lab — SOC & IT Administration Environment

A self-built enterprise-style home lab on Proxmox VE designed for hands-on learning in IT support, systems administration, security monitoring, and blue team operations.

## Architecture

**Proxmox Host:** axiom-core | 32 GB RAM | ~800 GB storage

### Network Segmentation

| Zone | Subnet | Bridge | Purpose |
|------|--------|--------|---------|
| SOC / Management | 10.10.10.0/24 | vmbr1 | SIEM, monitoring, admin tools |
| Corporate | 10.10.20.0/24 | vmbr2 | Domain controller, workstations, servers |
| Attack | 10.10.30.0/24 | vmbr3 | Isolated red team testing only |
| Home / Isolated | 192.168.1.0/24 | vmbr0 | Personal VMs, not part of lab domain |

### VM Inventory (12 VMs)

| VMID | Name | Zone | Role | OS |
|------|------|------|------|----|
| 100 | axiom-wazuh | SOC | Wazuh SIEM — Manager, Indexer, Dashboard | Ubuntu Server |
| 101 | axiom-ad | Corporate | Active Directory Domain Controller, DNS | Windows Server |
| 102 | axiom-client | Corporate | Domain-joined Windows workstation | Windows 10/11 |
| 103 | axiom-kali | Attack | Controlled attack simulation | Kali Linux |
| 104 | axiom-tools | SOC | Ticketing (GLPI), vulnerability scanner, utilities | Ubuntu Server |
| 107 | axiom-netlab | Corporate | Network diagnostics and troubleshooting | Ubuntu Server |
| 109 | axiom-splunk | SOC | Splunk Enterprise (Free) — secondary SIEM | Ubuntu Server |
| 110 | axiom-linux | Corporate | Linux administration practice target | Ubuntu Server |
| 111 | axiom-client2 | Corporate | Second domain-joined workstation | Windows 10 |

## Phase 1 — Complete

- Assessed and assigned purpose to all existing VMs
- Designed four-zone network segmentation (SOC, Corporate, Attack, Isolated)
- Created 3 new VMs: axiom-splunk (SIEM), axiom-linux (Linux admin), axiom-client2 (2nd workstation)
- Defined 20-lab build order from beginner to advanced
- Established GitHub portfolio structure

### Evidence
- [VM Inventory](Phase1-Evidence/vm-inventory.md)
- [Full VM Configurations](Phase1-Evidence/full-vm-configs.md) — All 12 VMs with specs, roles, and status
- [Network Zone Configuration](Phase1-Evidence/network-zones.md)
- [Network Architecture Diagram](Phase1-Evidence/network-diagram.md) — Visual topology with firewall policy
- [IP Address Plan](Phase1-Evidence/ip-address-plan.md) — Static IP assignments and DNS strategy
- [Storage & Resources](Phase1-Evidence/storage-and-resources.md)
- [New VMs Created](Phase1-Evidence/new-vms-created.md)
- [Wazuh SIEM Assessment](Phase1-Evidence/vm-assessment-wazuh.md) — Full stack confirmed (Manager + Indexer + Dashboard v4.7.5)
- [Active Directory Assessment](Phase1-Evidence/vm-assessment-ad.md) — Clean Windows Server, AD not yet installed
- [Security Audit](Phase1-Evidence/security-audit.md) — Infrastructure audit, firewall remediation, hardening roadmap

## Planned Labs

1. Active Directory — DC, DNS, users, groups, OUs, GPOs
2. SIEM — Wazuh + Splunk deployment, log ingestion, alerting
3. Windows Logging — Sysmon, PowerShell monitoring, Event Forwarding
4. IT Help Desk — Ticketing system, support workflows, runbooks
5. Vulnerability Management — Scanning, prioritization, remediation
6. Linux Administration — SSH hardening, services, cron, backups
7. Network Troubleshooting — Wireshark, DNS/DHCP diagnostics, packet analysis
8. Incident Response — Controlled attack simulations with full triage workflows
9. Detection Engineering — Sigma rules, MITRE ATT&CK mapping, tuning

## Skills Being Developed

- Active Directory & Group Policy
- SIEM deployment and log analysis (Wazuh, Splunk)
- Endpoint monitoring (Sysmon, Windows Event Logs)
- Vulnerability scanning and remediation
- IT support workflows and ticketing
- Linux and Windows server administration
- Network troubleshooting and packet analysis
- Incident detection, triage, and response
- MITRE ATT&CK framework

---

*Active development*
