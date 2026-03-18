# New VMs Created — Phase 1

Three new VMs created to support the full lab build.

## VM 109 — axiom-splunk
| Setting | Value |
|---------|-------|
| Cores | 4 |
| Memory | 8192 MB |
| Disk | 100 GB (local-lvm) |
| Network | vmbr1 (SOC zone, 10.10.10.0/24) |
| OS Type | Linux |
| ISO | ubuntu-24.04.4-live-server-amd64.iso |
| Role | Splunk Enterprise (Free) — secondary SIEM |

## VM 110 — axiom-linux
| Setting | Value |
|---------|-------|
| Cores | 2 |
| Memory | 2048 MB |
| Disk | 32 GB (local-lvm) |
| Network | vmbr2 (Corporate zone, 10.10.20.0/24) |
| OS Type | Linux |
| ISO | ubuntu-24.04.4-live-server-amd64.iso |
| Role | Linux administration practice target |

## VM 111 — axiom-client2
| Setting | Value |
|---------|-------|
| Cores | 2 |
| Memory | 4096 MB |
| Disk | 60 GB (local-lvm) |
| Network | vmbr2 (Corporate zone, 10.10.20.0/24) |
| OS Type | Windows 10 |
| BIOS | OVMF (UEFI) |
| Machine | q35 |
| ISO | Windows Server Eval |
| Role | Second domain-joined workstation |
