# VM Assessment — axiom-ad (VMID 101)

**Date:** 2026-03-18
**Method:** Offline disk inspection from Proxmox host (NTFS mounted read-only)

## Summary

| Property | Value |
|----------|-------|
| OS | Windows Server (Evaluation) |
| BIOS | OVMF (UEFI) |
| User Accounts | Administrator (default only) |
| Network | vmbr2 (Corporate zone, 10.10.20.0/24) |
| Network Config | **Not configured** — needs static IP |
| Disk | 64 GB (GPT: 200M EFI, 16M reserved, 63G data, 786M recovery) |
| RAM | 4096 MB |
| Cores | 2 |

## Active Directory Status: NOT INSTALLED

- **No NTDS database** (`Windows\NTDS\` directory does not exist)
- **No DNS Server role** (no dns.exe or DNS zones found)
- **No domain Group Policy** objects beyond defaults
- **Server Manager** is present (confirms Windows Server OS is installed)
- Only the default `Administrator` user account exists

## Assessment

Windows Server is installed and functional but is a **clean, unconfigured** installation. AD DS has never been promoted on this machine. This is ideal — we start Lab 1 from scratch.

## Next Steps (Lab 1: Active Directory Setup)

1. Boot VM, access via Proxmox console
2. Set static IP: 10.10.20.10/24, gateway 10.10.20.1, DNS 127.0.0.1
3. Set hostname to `axiom-ad` (if not already set)
4. Install AD DS role via Server Manager
5. Promote to Domain Controller — new forest: `axiom.local`
6. Configure DNS (integrated with AD)
7. Create OUs, users, and groups for lab scenarios
