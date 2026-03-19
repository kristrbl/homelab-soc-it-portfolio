# Full VM Configuration Reference

All 12 VM configurations as pulled from Proxmox API on 2026-03-18.

---

## VM 100 — axiom-wazuh (SOC Zone)

| Setting | Value |
|---------|-------|
| OS | Ubuntu 24.04.4 LTS |
| Cores | 4 |
| Memory | 8192 MB |
| Disk | 40 GB (LVM: ubuntu-vg/ubuntu-lv 19 GB) |
| Network | vmbr1 (10.10.10.0/24) |
| Services | Wazuh Manager 4.7.5, Wazuh Indexer 4.7.5, Wazuh Dashboard 4.7.5 |
| Status | OS installed, Wazuh installed, needs static IP |

## VM 101 — axiom-ad (Corporate Zone)

| Setting | Value |
|---------|-------|
| OS | Windows Server Evaluation |
| BIOS | OVMF (UEFI) |
| Cores | 2 |
| Memory | 4096 MB |
| Disk | 64 GB |
| Network | vmbr2 (10.10.20.0/24) |
| Status | OS installed, AD DS NOT installed — clean slate |

## VM 102 — axiom-client (Corporate Zone)

| Setting | Value |
|---------|-------|
| OS Type | Windows 11 |
| Cores | 2 |
| Memory | 4096 MB |
| Disk | 60 GB |
| Network | vmbr2 (10.10.20.0/24), NIC: e1000 |
| Status | Needs assessment |

## VM 103 — axiom-kali (Attack Zone)

| Setting | Value |
|---------|-------|
| OS Type | Linux |
| Cores | 2 |
| Memory | 4096 MB |
| Disk | 32 GB |
| Network | vmbr3 (10.10.30.0/24) |
| Status | Needs assessment |

## VM 104 — axiom-tools (SOC Zone)

| Setting | Value |
|---------|-------|
| OS Type | Linux |
| Cores | 2 |
| Memory | 4096 MB |
| Disk | 40 GB |
| Network | vmbr1 (10.10.10.0/24) |
| Status | Needs assessment |

## VM 105 — axiom-athena (Isolated Zone)

| Setting | Value |
|---------|-------|
| OS | Ubuntu 24.04 |
| Cores | 4 |
| Memory | 12288 MB |
| Disk | 80 GB |
| Network | vmbr0 (192.168.1.0/24) |
| Services | Ollama (port 11434), ATHENA AI assistant |
| Status | Running — DO NOT TOUCH |

## VM 106 — axiom-forge (Isolated Zone)

| Setting | Value |
|---------|-------|
| OS Type | Unknown |
| Cores | 4 |
| Memory | 6144 MB |
| Disk | 60 GB |
| Network | vmbr0 (192.168.1.0/24) |
| Status | Minecraft server — DO NOT TOUCH |

## VM 107 — axiom-netlab (Corporate Zone)

| Setting | Value |
|---------|-------|
| OS Type | Linux |
| Cores | 2 |
| Memory | 2048 MB |
| Disk | 52 GB |
| Network | vmbr2 (10.10.20.0/24) |
| Status | Needs assessment |

## VM 108 — athena-shield (Isolated Zone)

| Setting | Value |
|---------|-------|
| OS Type | Linux |
| Cores | 4 |
| Memory | 8192 MB |
| Disk | 100 GB (SSD emulation, discard enabled) |
| Network | vmbr0 (192.168.1.0/24), Proxmox firewall enabled on NIC |
| Description | "Security enforcement and telemetry VM. Zero-trust, deny-by-default." |
| Boot | onboot=1 (starts with host) |
| Status | Running — all ports filtered, unreachable via SSH |

## VM 109 — axiom-splunk (SOC Zone)

| Setting | Value |
|---------|-------|
| OS Type | Linux |
| Cores | 4 |
| Memory | 8192 MB |
| Disk | 100 GB |
| Network | vmbr1 (10.10.10.0/24) |
| ISO | ubuntu-24.04.4-live-server-amd64.iso |
| Status | Created, needs OS install |

## VM 110 — axiom-linux (Corporate Zone)

| Setting | Value |
|---------|-------|
| OS Type | Linux |
| Cores | 2 |
| Memory | 2048 MB |
| Disk | 32 GB |
| Network | vmbr2 (10.10.20.0/24) |
| ISO | ubuntu-24.04.4-live-server-amd64.iso |
| Status | Created, needs OS install |

## VM 111 — axiom-client2 (Corporate Zone)

| Setting | Value |
|---------|-------|
| OS Type | Windows 10 |
| BIOS | OVMF (UEFI), q35 machine |
| Cores | 2 |
| Memory | 4096 MB |
| Disk | 60 GB |
| Network | vmbr2 (10.10.20.0/24) |
| ISO | Windows Server Eval (needs Win10 Pro ISO) |
| Status | Created, needs OS install |
