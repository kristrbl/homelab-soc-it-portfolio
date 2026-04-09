# Screenshot Evidence Checklist

Use this before marking any lab as complete. Every lab needs screenshots to be credible.

---

## Screenshot Standards

- **Redact before publishing:** Home network IPs (192.168.1.x), personal email, credentials
- **Lab IPs are fine:** 10.10.x.x ranges used in the lab subnet
- **Never screenshot passwords** — blur or crop them
- **Include hostname/timestamp** where possible so it's clear the screenshot is from your lab
- **File naming:** `[lab-shortname]-[##]-[description].png`
  - Example: `ad-lab-01-ou-structure.png`
  - Example: `wazuh-03-brute-force-alert-detail.png`

---

## Per-Lab Minimum Screenshot Count

| Lab | Minimum | What to Capture |
|---|---|---|
| Active Directory | 6 | Role install, OU structure, user account, GPO linked, domain join, account unlock |
| Windows Troubleshooting | 4 | Tool output per scenario, error identified, resolution applied |
| Wazuh SIEM | 5 | Dashboard agents active, alert fired, alert detail, raw log, cross-reference |
| Brute Force Detection | 4 | Attack running, Wazuh alert, Event Viewer 4625 cluster, incident note |
| User Provisioning | 4 | User created, group membership, unlock, offboarding (disabled + moved) |
| Linux Auth Log | 3 | grep output with results, `last` output, findings documented |

---

## General Screenshot Checklist

Before publishing any lab:

- [ ] At least the minimum number of screenshots are in the `SCREENSHOTS/` folder
- [ ] Files are named using the naming convention
- [ ] No home network IPs visible (192.168.1.x)
- [ ] No plaintext passwords or credentials visible
- [ ] Screenshots match the steps described in the README
- [ ] Screenshots are legible at normal zoom (not too small, not blurry)
- [ ] Hostname is visible in at least one screenshot per lab

---

## Screenshot Naming Convention

```
[lab-id]-[sequence]-[description].png

Examples:
  ad-lab-01-dc-role-installed.png
  ad-lab-02-ou-structure-aduc.png
  ad-lab-03-new-user-account.png
  wazuh-01-dashboard-agents-active.png
  wazuh-02-brute-force-alert-fired.png
  win-ts-01-ipconfig-apipa-address.png
  win-ts-02-task-manager-high-cpu.png
  linux-auth-01-failed-logins-by-ip.png
```
