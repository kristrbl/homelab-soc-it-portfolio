# Active Directory Home Lab

**Track:** IT Support | Systems Administration | Help Desk
**Status:** 🔧 In Progress

---

## Scenario

A small company is setting up a Windows domain environment for the first time. As the IT technician, you are responsible for deploying and configuring a domain controller, building an organizational structure that reflects the business, creating user accounts for all employees, applying Group Policy to manage desktop settings, and verifying that users can log in and access appropriate resources.

This lab simulates the full setup and ongoing administration that an IT support tech or junior sysadmin would handle in a real SMB environment.

---

## Why This Matters for an IT Role

Active Directory is the backbone of Windows enterprise environments. Nearly every IT support, help desk, and sysadmin role involves AD in some capacity — resetting passwords, unlocking accounts, creating new users, troubleshooting login failures, or applying GPOs. Being able to build it from scratch and administer it shows you understand how it works, not just how to click through it.

---

## Lab Environment

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE (home lab) |
| Domain Controller | Windows Server 2022 — VM |
| Client Machine | Windows 10 — VM |
| Network | Isolated internal subnet (10.10.20.0/24) |
| Domain | `corp.local` |

---

## Tools Used

- Windows Server 2022
- Active Directory Domain Services (AD DS)
- DNS Server (installed with AD DS)
- Active Directory Users and Computers (ADUC)
- Group Policy Management Console (GPMC)
- PowerShell
- Windows 10 client VM

---

## Implementation

### Phase 1 — Deploy the Domain Controller

1. Install Windows Server 2022 on a VM (set static IP, rename machine to `DC01`)
2. Install the AD DS role via Server Manager → Add Roles and Features
3. Promote to domain controller — create new forest: `corp.local`
4. Set DSRM password, configure DNS, complete promotion and reboot
5. Verify AD DS and DNS services are running
6. Confirm DNS forward lookup zone exists for `corp.local`

**Commands used:**
```powershell
# Verify AD DS installation
Get-WindowsFeature AD-Domain-Services

# Verify domain controller
Get-ADDomainController

# Check DNS zones
Get-DnsServerZone
```

### Phase 2 — Build Organizational Unit Structure

Design an OU structure that reflects a small business:

```
corp.local
├── IT
│   ├── Computers
│   └── Users
├── HR
│   ├── Computers
│   └── Users
├── Finance
│   ├── Computers
│   └── Users
├── Management
│   └── Users
└── ServiceAccounts
```

Create OUs using ADUC or PowerShell:
```powershell
New-ADOrganizationalUnit -Name "IT" -Path "DC=corp,DC=local"
New-ADOrganizationalUnit -Name "Users" -Path "OU=IT,DC=corp,DC=local"
New-ADOrganizationalUnit -Name "Computers" -Path "OU=IT,DC=corp,DC=local"
# Repeat for HR, Finance, Management, ServiceAccounts
```

### Phase 3 — Create User Accounts

Create at least 10 test users across departments:

```powershell
New-ADUser `
  -Name "Jane Smith" `
  -GivenName "Jane" `
  -Surname "Smith" `
  -SamAccountName "jsmith" `
  -UserPrincipalName "jsmith@corp.local" `
  -Path "OU=Users,OU=HR,DC=corp,DC=local" `
  -AccountPassword (ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force) `
  -Enabled $true
```

Create security groups and add users:
```powershell
New-ADGroup -Name "IT-Staff" -GroupScope Global -Path "OU=IT,DC=corp,DC=local"
Add-ADGroupMember -Identity "IT-Staff" -Members "jdoe"
```

### Phase 4 — Configure Group Policy

Apply the following baseline GPOs:

| GPO Name | Scope | Setting |
|---|---|---|
| Desktop Lockout | Domain | Lock screen after 10 minutes |
| Password Policy | Domain | Min 12 chars, complexity required, 90-day expiry |
| Disable USB Storage | IT Computers | Removable storage — deny write access |
| Map Network Drive | All Users | Map \\DC01\shared to Z: |
| Restrict Control Panel | HR, Finance | Prevent access to Control Panel |

```powershell
# Create and link a GPO
New-GPO -Name "Baseline-Password-Policy" | New-GPLink -Target "DC=corp,DC=local"

# Force GPO refresh on client
gpupdate /force
```

### Phase 5 — Join Client and Verify

1. Join Windows 10 VM to `corp.local`
2. Log in with a domain user account
3. Verify GPOs are applying: `gpresult /r`
4. Verify drive mapping is working
5. Test account lockout by entering wrong password 5 times
6. Unlock account from DC: `Unlock-ADAccount -Identity "jsmith"`

---

## Evidence to Collect

- [ ] Screenshot: AD DS role installed in Server Manager
- [ ] Screenshot: OU structure in ADUC
- [ ] Screenshot: User account properties (without password visible)
- [ ] Screenshot: GPO linked to domain in GPMC
- [ ] Screenshot: `gpresult /r` output on client VM
- [ ] Screenshot: Client VM joined to domain (System Properties → domain name shown)
- [ ] Screenshot: Locked account in ADUC and unlock process
- [ ] Screenshot: Shared drive mapped on client

---

## Screenshot Naming Convention

```
ad-lab-01-adcs-role-installed.png
ad-lab-02-ou-structure.png
ad-lab-03-user-account-properties.png
ad-lab-04-gpo-linked.png
ad-lab-05-gpresult-output.png
ad-lab-06-domain-join-confirmed.png
ad-lab-07-account-unlock.png
ad-lab-08-drive-mapped.png
```

---

## Interview Talking Points

**"Tell me about your experience with Active Directory."**
> I deployed a full AD environment in my homelab — stood up a Windows Server 2022 domain controller, built an OU structure for a simulated small company, created user accounts and security groups using both ADUC and PowerShell, and applied Group Policy for password policy, screen lock, and drive mapping. I also walked through common support tasks like account unlocks, password resets, and troubleshooting GPO application using gpresult.

**"How do you reset a user's password in AD?"**
> In ADUC, right-click the user → Reset Password. In PowerShell: `Set-ADAccountPassword -Identity "jsmith" -Reset -NewPassword (Read-Host -AsSecureString)` then `Set-ADUser -Identity "jsmith" -ChangePasswordAtLogon $true` to force a change at next login.

**"What's the difference between a Security Group and a Distribution Group?"**
> Security groups are used for access control — assigning permissions to files, folders, or GPOs. Distribution groups are used for email only — they can't be used to assign permissions.

---

## Beginner Mistakes to Avoid

- Forgetting to set a static IP on the DC before promotion (DHCP will cause DNS issues)
- Not linking GPOs to the correct scope — creating a GPO and linking it to the domain vs. a specific OU has very different effects
- Forgetting `gpupdate /force` when testing GPO changes — the client won't pick up new settings automatically
- Creating users in the default `Users` container instead of OUs — makes administration harder

---

## What This Proves to an Employer

- You understand Active Directory architecture and can deploy it from scratch
- You can perform the most common daily AD tasks: user creation, password reset, account unlock, group membership
- You know how Group Policy works and can configure and troubleshoot GPO application
- You can use PowerShell for AD administration — not just the GUI
- You understand domain join and can troubleshoot client-to-domain connectivity
