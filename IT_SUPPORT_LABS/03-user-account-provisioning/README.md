# User Account Provisioning Workflow

**Track:** IT Support | Help Desk | Systems Administration
**Status:** 📋 Planned

---

## Scenario

Simulate the complete lifecycle of a user account: new hire onboarding, role change, and offboarding. This lab documents the exact steps an IT support technician follows when a new employee starts, gets promoted or transferred, and eventually leaves the company. Each step maps directly to a ticket type that help desk teams handle daily.

---

## Why This Matters for a Help Desk Role

User provisioning and deprovisioning is one of the highest-volume tasks in IT support. Every new hire triggers an account creation ticket. Every departure triggers a termination ticket. Getting these steps wrong — like leaving an account active after someone leaves — creates real security risks. Employers want to know you follow a documented process and understand why each step matters.

---

## Lab Environment

| Component | Details |
|---|---|
| Domain Controller | Windows Server 2022 — `corp.local` |
| Client | Windows 10 VM |
| Tools | ADUC, PowerShell, Group Policy |

---

## Workflow 1 — New Hire Onboarding

**Ticket received:** "New employee Jane Smith starts Monday. Role: HR Coordinator. Manager: Bob Jones."

**Steps:**

```powershell
# 1. Create the user account
New-ADUser `
  -Name "Jane Smith" `
  -GivenName "Jane" `
  -Surname "Smith" `
  -SamAccountName "jsmith" `
  -UserPrincipalName "jsmith@corp.local" `
  -Title "HR Coordinator" `
  -Department "Human Resources" `
  -Manager "bjones" `
  -Path "OU=Users,OU=HR,DC=corp,DC=local" `
  -AccountPassword (ConvertTo-SecureString "Welcome2024!" -AsPlainText -Force) `
  -ChangePasswordAtLogon $true `
  -Enabled $true

# 2. Add to appropriate security groups
Add-ADGroupMember -Identity "HR-Staff" -Members "jsmith"
Add-ADGroupMember -Identity "All-Employees" -Members "jsmith"

# 3. Verify account
Get-ADUser -Identity "jsmith" -Properties *
```

**Manual checklist:**
- [ ] Account created in correct OU
- [ ] Password set — user must change at first login
- [ ] Added to correct security groups
- [ ] Email account provisioned (out of scope for this lab — document the step)
- [ ] Shared drive access confirmed
- [ ] Manager notified

---

## Workflow 2 — Password Reset

**Ticket received:** "User jsmith is locked out. Can't log in."

```powershell
# Check account status
Get-ADUser -Identity "jsmith" -Properties LockedOut, BadLogonCount, LastBadPasswordAttempt

# Unlock the account
Unlock-ADAccount -Identity "jsmith"

# Reset password if needed
Set-ADAccountPassword -Identity "jsmith" `
  -Reset `
  -NewPassword (ConvertTo-SecureString "TempPass123!" -AsPlainText -Force)

# Force password change at next login
Set-ADUser -Identity "jsmith" -ChangePasswordAtLogon $true

# Verify
Get-ADUser -Identity "jsmith" -Properties LockedOut
```

**Ticket note format:**
```
User: jsmith (Jane Smith) — HR
Issue: Account locked out after multiple failed login attempts
Action: Unlocked account, temporary password issued
Resolution: User confirmed able to log in. Password change prompted.
Closed.
```

---

## Workflow 3 — Role Change / Department Transfer

**Ticket received:** "Jane Smith has transferred to IT department. Update her access accordingly."

```powershell
# Move user to new OU
Move-ADObject `
  -Identity "CN=Jane Smith,OU=Users,OU=HR,DC=corp,DC=local" `
  -TargetPath "OU=Users,OU=IT,DC=corp,DC=local"

# Update department and title
Set-ADUser -Identity "jsmith" `
  -Department "Information Technology" `
  -Title "IT Support Technician"

# Remove old group, add new group
Remove-ADGroupMember -Identity "HR-Staff" -Members "jsmith" -Confirm:$false
Add-ADGroupMember -Identity "IT-Staff" -Members "jsmith"

# Verify
Get-ADUser -Identity "jsmith" -Properties Department, Title, MemberOf
```

---

## Workflow 4 — Offboarding / Account Termination

**Ticket received:** "Jane Smith's last day is Friday. Please process offboarding."

```powershell
# 1. Disable immediately (do not delete — keep for audit)
Disable-ADAccount -Identity "jsmith"

# 2. Remove from all security groups (preserves audit trail)
$groups = Get-ADUser -Identity "jsmith" -Properties MemberOf | Select-Object -ExpandProperty MemberOf
foreach ($group in $groups) {
    Remove-ADGroupMember -Identity $group -Members "jsmith" -Confirm:$false
}

# 3. Move to Disabled Users OU
Move-ADObject `
  -Identity "CN=Jane Smith,OU=Users,OU=IT,DC=corp,DC=local" `
  -TargetPath "OU=Disabled,DC=corp,DC=local"

# 4. Update description with termination date
Set-ADUser -Identity "jsmith" -Description "TERMINATED: 2024-04-12 | Offboarded by IT"

# 5. Verify account is disabled
Get-ADUser -Identity "jsmith" -Properties Enabled, Description
```

**Manual checklist:**
- [ ] Account disabled
- [ ] All group memberships removed
- [ ] Account moved to Disabled OU
- [ ] Manager notified
- [ ] Email forwarding set (or mailbox disabled) — document step
- [ ] Physical assets return noted (laptop, badge)
- [ ] Account scheduled for deletion after 90-day retention period

---

## Evidence to Collect

- [ ] Screenshot: New user created in correct OU in ADUC
- [ ] Screenshot: Group membership before and after transfer
- [ ] Screenshot: Locked account status in ADUC and unlock confirmation
- [ ] Screenshot: Disabled account in Disabled OU with description set
- [ ] Screenshot: PowerShell output confirming each major step

---

## Interview Talking Points

**"How do you handle user offboarding?"**
> I follow a documented checklist. First, disable the account immediately — don't delete it, because you may need it for audit purposes. Then remove group memberships, move the account to a Disabled OU, and update the description with the termination date. I also coordinate on email access, asset return, and make sure nothing gets missed. The key is that everything is documented so there's a clear record.

**"Why do you disable accounts instead of deleting them?"**
> Two reasons: audit trail and recovery. If something comes up after the person leaves — an investigation, an HR matter, a compliance request — you need to show when the account was active and what it had access to. Deletion destroys that. You also occasionally get someone who comes back, and recreating an account is more work than re-enabling one. Our policy is to disable immediately, remove access, and delete after 90 days.

---

## What This Proves to an Employer

- You understand the full user lifecycle, not just creation
- You follow documented processes with audit trail preservation
- You know how to use PowerShell for account management, not just the GUI
- You understand the security implications of improper offboarding
- You can write clear ticket notes that document your actions
