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

## Labs

| # | Lab | Status |
|---|-----|--------|
| 1 | [Active Directory](Labs/01-Active-Directory/) — DC, DNS, OUs, users, groups, GPOs | In Progress |

## Skills Being Developed

- Active Directory & Group Policy
- Windows Server administration
- DNS configuration
- Enterprise network segmentation

---

*Active development*
