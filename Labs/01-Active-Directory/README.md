# Lab 1 — Active Directory Domain Controller Setup

## Objective

Deploy a fully functional Active Directory environment with a Domain Controller, DNS, organizational units, users, groups, and Group Policy Objects — simulating a small enterprise network.

## Environment

| VM | Name | Role | OS | IP | Zone |
|----|------|------|----|----|------|
| 101 | axiom-polaris | Domain Controller + DNS | Windows Server 2025 Eval | 10.10.20.10 | Corporate (vmbr2) |
| 102 | axiom-client | Domain-joined workstation | Windows 10/11 | 10.10.20.50 | Corporate (vmbr2) |
| 111 | axiom-client2 | Domain-joined workstation | Windows 10/11 | 10.10.20.51 | Corporate (vmbr2) |

**Domain:** axiom.local
**NetBIOS:** AXIOM

## Skills Demonstrated

- Windows Server installation and initial configuration
- Active Directory Domain Services (AD DS) deployment
- DNS server configuration
- Organizational Unit (OU) design
- User and group management
- Group Policy Object (GPO) creation and linking
- Domain join process for Windows clients

## Architecture

```
        axiom-polaris (DC)
        10.10.20.10
        axiom.local
            |
     -------+-------
     |              |
axiom-client    axiom-client2
10.10.20.50     10.10.20.51
(Help Desk)     (Finance)
```

## Steps Performed

### Phase 1: Windows Server Setup
- [ ] Boot axiom-polaris
- [ ] Set static IP: 10.10.20.10/24
- [ ] Set hostname: AXIOM-POLARIS
- [ ] Set DNS to 127.0.0.1 (will be its own DNS server)

### Phase 2: AD DS Installation
- [ ] Install AD DS role via Server Manager
- [ ] Promote to Domain Controller
- [ ] Create new forest: axiom.local
- [ ] Verify DNS zones created automatically
- [ ] Verify SYSVOL and NETLOGON shares

### Phase 3: Organizational Units
- [ ] Create OU structure:
  - axiom.local
    - OU=Departments
      - OU=IT
      - OU=Finance
      - OU=HR
    - OU=Servers
    - OU=Workstations
    - OU=Service-Accounts
    - OU=Disabled-Users

### Phase 4: Users and Groups
- [ ] Create security groups: IT-Admins, Help-Desk, Finance-Users, HR-Users
- [ ] Create users:
  - k.thompson (IT-Admins) — IT Administrator
  - j.martinez (Help-Desk) — Help Desk Technician
  - s.chen (Finance-Users) — Finance Analyst
  - m.johnson (HR-Users) — HR Coordinator
- [ ] Assign users to appropriate OUs and groups

### Phase 5: Group Policy Objects
- [ ] Password policy: 12 char min, complexity, 90-day expiry
- [ ] Account lockout: 5 attempts, 30-min lockout
- [ ] Disable USB storage (workstations)
- [ ] Map network drives by department
- [ ] Set wallpaper with company branding (optional)

### Phase 6: Domain Join
- [ ] Join axiom-client to axiom.local
- [ ] Join axiom-client2 to axiom.local
- [ ] Test user login on each workstation
- [ ] Verify GPOs are applying (gpresult /r)

## Screenshots

> Add screenshots to `../../Screenshots/Active-Directory/` as you complete each phase.

| Screenshot | Description |
|------------|-------------|
| `01-server-ip-config.png` | Static IP configuration on axiom-polaris |
| `02-ad-ds-install.png` | AD DS role installation wizard |
| `03-promote-dc.png` | Domain Controller promotion |
| `04-dns-zones.png` | DNS forward and reverse lookup zones |
| `05-ou-structure.png` | Organizational Units in AD Users and Computers |
| `06-users-created.png` | User accounts in their respective OUs |
| `07-gpo-list.png` | Group Policy Objects linked to OUs |
| `08-domain-join.png` | Successful domain join on axiom-client |
| `09-gpresult.png` | gpresult /r showing applied policies |

## Problems Encountered

> Document any issues here as you go. Example:
> - "DNS resolution failed after DC promotion — fixed by setting DNS to 127.0.0.1"

## What I Learned

> Fill this in after completing the lab. Think about:
> - Why does the DC need to be its own DNS server?
> - What's the difference between a security group and a distribution group?
> - Why use OUs instead of just putting everyone in the default Users container?

## Security Notes

- Default Administrator account renamed and strong password set
- Guest account disabled
- Password policy enforced via GPO (not default domain policy)
- Service accounts in dedicated OU with restricted permissions

## Resume Bullet

> "Deployed Active Directory Domain Services on Windows Server 2025 with DNS, designed OU structure for 4 departments, created and linked GPOs for password policy, account lockout, and endpoint restrictions, and domain-joined Windows workstations"

## Interview Talking Points

- "I set up a full AD environment from scratch — DC, DNS, OUs, users, groups, and GPOs."
- "I designed the OU structure to mirror a real company with IT, Finance, and HR departments."
- "I enforced security baselines through Group Policy — password complexity, account lockout thresholds, and USB restrictions."
- "When I joined workstations to the domain, I verified GPO application using gpresult and troubleshot DNS resolution issues."
