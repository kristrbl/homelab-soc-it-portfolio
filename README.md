# Kristian Todman — IT Support & Defensive Security Portfolio

**IT Support Specialist | Help Desk Technician | Junior SOC Analyst**

A hands-on technical portfolio built through real homelab work on a self-built Proxmox VE server. Every project was designed, configured, documented, and tested against real VMs — not simulated or theoretical. This portfolio demonstrates practical skills across IT support, endpoint management, Windows/Linux administration, network troubleshooting, and defensive security operations.

> **Status:** Actively building. Labs are published as they are completed. See [PROJECT_STATUS.md](PROJECT_STATUS.md) for current progress.

---

## Lab Architecture

Built on a Proxmox VE hypervisor with 15 VMs across 4 isolated network zones. Each zone serves a distinct purpose and maps directly to real enterprise network segmentation concepts.

```
┌─────────────────────────────────────────────────────────┐
│  PROXMOX HOST — gw-nexus                                │
│  15 VMs | Quad-zone network segmentation                │
│                                                         │
│  ┌─────────────────┐   ┌─────────────────┐             │
│  │  ZONE 1 — SOC   │   │  ZONE 2 — CORP  │             │
│  │  vmbr1          │   │  vmbr2          │             │
│  │                 │   │                 │             │
│  │ Wazuh SIEM      │   │ AD Domain Ctrl  │             │
│  │ Splunk SIEM     │   │ Win 11 Client   │             │
│  │ IR/Ticketing    │   │ Win 10 Client   │             │
│  └────────┬────────┘   │ Ubuntu Server   │             │
│           │            │ Network Lab VM  │             │
│  ┌────────▼────────┐   └────────┬────────┘             │
│  │  ZONE 0 — MGMT  │           │                       │
│  │  vmbr0          │   ┌────────▼────────┐             │
│  │                 │   │  ZONE 3 — ATCK  │             │
│  │ Main AI VM      │   │  vmbr3          │             │
│  │ Honeypot        │   │                 │             │
│  └─────────────────┘   │ Kali Linux      │             │
│                        │ Adversary Sim   │             │
│                        └─────────────────┘             │
│                                                         │
│  Inter-zone firewall VM bridges all 4 zones             │
└─────────────────────────────────────────────────────────┘
```

| VM | Role | OS | Zone |
|---|---|---|---|
| AD Domain Controller | Identity, GPO, DNS, user management | Windows Server 2025 | Corp |
| Windows 11 Workstation | Domain-joined endpoint, GPO target | Windows 11 | Corp |
| Windows 10 Workstation | Second endpoint for multi-target labs | Windows 10 | Corp |
| Ubuntu Server | Linux admin, Wazuh agent, hardening | Ubuntu Server | Corp |
| Wazuh SIEM | All-source threat detection, alert triage | Linux | SOC |
| Splunk SIEM | Log analysis, correlation, forensic timelines | Linux | SOC |
| IR / Ticketing | GLPI help desk, vulnerability scanning | Linux | SOC |
| Network Lab VM | Packet analysis, routing/switching labs | Linux | Corp |
| Kali Linux | Controlled attack simulation for detection labs | Linux | Attacker |
| Adversary Sim | AI-driven attack generation for detection content | Linux | Attacker |
| Inter-zone Firewall | Segments all 4 network zones, logs all drops | Linux | All |

---

## Skills

| Category | Skills |
|---|---|
| **IT Support** | Troubleshooting, ticket handling, user account management, password resets, endpoint support, remote support workflows |
| **Operating Systems** | Windows 10/11, Windows Server 2025, Ubuntu Server |
| **Active Directory** | User provisioning, OU structure, Group Policy, account lifecycle, DNS, access control |
| **Networking** | TCP/IP, DNS, DHCP, VLANs, subnetting, firewall rules, network troubleshooting, packet analysis |
| **Security Monitoring** | Wazuh SIEM, Splunk, alert triage, log analysis, Windows Event Logs, Linux auth logs, detection logic |
| **Scripting** | PowerShell (support automation), Bash (Linux administration) |
| **Documentation** | Incident reports, ticket notes, troubleshooting logs, runbooks, change logs |
| **Virtualization** | Proxmox VE — VM provisioning, network segmentation, snapshot management |
| **Tools** | Wazuh, Splunk, Wireshark, Nmap, Nessus Essentials, GLPI, Kali Linux, Event Viewer, Sysinternals Suite |

---

## Featured Projects

| # | Project | Track | Status |
|---|---|---|---|
| 1 | [Active Directory Home Lab](IT_SUPPORT_LABS/01-active-directory-lab/) | IT Support / SysAdmin | ✅ Complete |
| 2 | [Windows Troubleshooting Lab](IT_SUPPORT_LABS/02-windows-troubleshooting-lab/) | IT Support / Help Desk | 🔧 In Progress |
| 3 | [Wazuh SIEM Setup & Alert Investigation](SOC_LABS/01-wazuh-siem-setup/) | SOC / Defensive Security | 🔧 In Progress |
| 4 | [Brute Force Detection Scenario](SOC_LABS/03-brute-force-detection/) | SOC / Defensive Security | 🔧 In Progress |
| 5 | [User Account Provisioning Workflow](IT_SUPPORT_LABS/03-user-account-provisioning/) | IT Support / Help Desk | 📋 Planned |
| 6 | [Linux Auth Log Review](SOC_LABS/07-linux-auth-log-review/) | SOC / Linux | 📋 Planned |

---

## Repository Map

```
homelab-soc-it-portfolio/
│
├── README.md                        ← You are here
├── ABOUT_ME.md                      ← Background and goals
├── PROJECT_STATUS.md                ← Live progress tracker
├── ROADMAP.md                       ← Completion timeline
│
├── IT_SUPPORT_LABS/                 ← Help Desk / IT Support projects
│   ├── 01-active-directory-lab/     ← ✅ Complete
│   ├── 02-windows-troubleshooting-lab/
│   ├── 03-user-account-provisioning/
│   ├── 04-helpdesk-ticketing-simulation/
│   ├── 05-remote-support-workflow/
│   ├── 06-powershell-support-scripts/
│   ├── 07-endpoint-hardening/
│   ├── 08-patch-management-sim/
│   ├── 09-file-share-permissions/
│   └── 10-backup-restore-test/
│
├── SOC_LABS/                        ← SOC / Defensive Security projects
│   ├── 01-wazuh-siem-setup/
│   ├── 02-log-analysis-lab/         ← Uses Splunk (DEEPDIVE)
│   ├── 03-brute-force-detection/
│   ├── 04-phishing-investigation/
│   ├── 05-vulnerability-scanning/
│   ├── 06-windows-event-log-investigation/
│   ├── 07-linux-auth-log-review/
│   ├── 08-incident-triage-process/
│   ├── 09-detection-mitre-mapping/
│   └── 10-containment-remediation-playbook/
│
├── NETWORKING_LABS/
├── WINDOWS_LABS/
├── LINUX_LABS/
├── DOCUMENTATION_TEMPLATES/
├── SCREENSHOTS/
├── CERTIFICATIONS/
├── STUDY_TRACKER/
└── RESUME/
```

---

## Current Focus

- Windows troubleshooting lab — documenting 6 real scenarios on Windows 11 workstation
- Wazuh SIEM alert investigations — brute force detection pipeline with Kali attack simulation
- Splunk log analysis lab
- Working toward CompTIA A+

---

## Certifications & Study Progress

| Certification | Status |
|---|---|
| CompTIA A+ | 📚 Studying |
| CompTIA Network+ | 📋 Planned |
| CompTIA Security+ | 📋 Planned |
| Google IT Support Certificate | 📋 Planned |

---

## Tools Used Across This Portfolio

`Active Directory` `Windows Server 2025` `PowerShell` `Wazuh` `Splunk` `Wireshark`
`Nmap` `Nessus Essentials` `Ubuntu Server` `Proxmox VE` `Event Viewer` `Kali Linux`
`GLPI` `Bash` `Group Policy Management` `Sysinternals Suite` `Sysmon`

---

## Contact

- **GitHub:** [github.com/kristrbl](https://github.com/kristrbl)
- **LinkedIn:** *(add your LinkedIn URL)*
- **Email:** *(add your contact email)*

---

*All labs are hands-on, self-documented, and designed to be walkable in a technical interview.*
