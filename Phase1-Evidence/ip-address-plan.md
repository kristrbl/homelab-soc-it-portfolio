# IP Address Plan

Static IP assignments for all lab VMs. Gateway for each zone is `.1` on the Proxmox host.

## vmbr0 — Home / Isolated (192.168.1.0/24)

| VMID | Hostname | IP | Role | Notes |
|------|----------|-----|------|-------|
| — | axiom-core | 192.168.1.50 | Proxmox host | Static, gateway 192.168.1.1 |
| 105 | axiom-athena | DHCP | AI assistant node | DO NOT TOUCH |
| 106 | axiom-forge | DHCP | Minecraft server | DO NOT TOUCH |
| 108 | athena-shield | DHCP | Security/telemetry | Zero-trust, deny-by-default |

## vmbr1 — SOC / Management (10.10.10.0/24)

| VMID | Hostname | IP | Role | Status |
|------|----------|-----|------|--------|
| — | axiom-core | 10.10.10.1 | Gateway | Active |
| 100 | axiom-wazuh | 10.10.10.10 | Wazuh SIEM (Manager + Indexer + Dashboard) | Needs static IP |
| 104 | axiom-tools | 10.10.10.20 | GLPI ticketing, vulnerability scanner | Needs config |
| 109 | axiom-splunk | 10.10.10.30 | Splunk Enterprise | Needs OS install |

## vmbr2 — Corporate (10.10.20.0/24)

| VMID | Hostname | IP | Role | Status |
|------|----------|-----|------|--------|
| — | axiom-core | 10.10.20.1 | Gateway | Active |
| 101 | axiom-ad | 10.10.20.10 | AD Domain Controller, DNS | Needs static IP |
| 102 | axiom-client | 10.10.20.20 | Windows workstation 1 | Needs config |
| 111 | axiom-client2 | 10.10.20.21 | Windows workstation 2 | Needs OS install |
| 110 | axiom-linux | 10.10.20.30 | Linux admin target | Needs OS install |
| 107 | axiom-netlab | 10.10.20.40 | Network troubleshooting | Needs config |

## vmbr3 — Attack (10.10.30.0/24)

| VMID | Hostname | IP | Role | Status |
|------|----------|-----|------|--------|
| — | axiom-core | 10.10.30.1 | Gateway | Active |
| 103 | axiom-kali | 10.10.30.10 | Kali Linux — red team | Needs config |

## DNS Plan

Once axiom-ad is promoted to Domain Controller:
- Domain: `axiom.local`
- Primary DNS: 10.10.20.10 (axiom-ad)
- All corporate zone VMs use axiom-ad as DNS
- SOC zone VMs use 10.10.20.10 or external DNS (8.8.8.8)
- Attack zone uses external DNS only (isolated from AD)
