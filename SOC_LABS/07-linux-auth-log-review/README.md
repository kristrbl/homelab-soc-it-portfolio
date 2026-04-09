# Linux Auth Log Review

**Track:** SOC | Defensive Security | Log Analysis
**Status:** 📋 Planned

---

## Scenario

Review `/var/log/auth.log` on an Ubuntu Server to identify suspicious authentication activity. The lab generates realistic log data through both normal activity and simulated events (failed SSH logins, sudo usage, new user creation). The goal is to learn how to read the log, identify patterns worth investigating, extract key indicators, and write up findings.

---

## Why This Matters for a SOC Role

Linux servers are everywhere — web servers, databases, SIEM backends, cloud instances. Auth log review is a fundamental defensive skill. A tier-1 analyst who can read `/var/log/auth.log` and identify a brute force attempt, a successful root login, or an unusual sudo escalation is immediately more useful than one who can't. This lab builds that muscle.

---

## Lab Environment

| Component | Details |
|---|---|
| Linux Server | Ubuntu Server 22.04 VM |
| Access | SSH from host or another VM |
| Log location | `/var/log/auth.log` |

---

## Tools Used

- `grep`, `awk`, `cut`, `sort`, `uniq`, `tail`, `head`
- `journalctl` (systemd log tool)
- `last` and `lastb` commands
- `who` and `w`
- Wazuh agent (optional — correlate with SIEM)

---

## Understanding the Log

Before investigating, understand what you're reading.

**Common auth.log entries:**

```
# Successful SSH login
Apr  9 10:15:22 ubuntu-srv sshd[2341]: Accepted password for kris from 10.10.20.10 port 52411 ssh2

# Failed SSH login
Apr  9 10:16:01 ubuntu-srv sshd[2342]: Failed password for root from 10.10.30.55 port 44892 ssh2

# Invalid user attempt
Apr  9 10:16:04 ubuntu-srv sshd[2343]: Invalid user admin from 10.10.30.55 port 44895

# Successful sudo usage
Apr  9 10:20:15 ubuntu-srv sudo: kris : TTY=pts/0 ; PWD=/home/kris ; USER=root ; COMMAND=/usr/bin/apt update

# New user created
Apr  9 11:00:01 ubuntu-srv useradd[3412]: new user: name=backdoor, UID=1001, GID=1001, home=/home/backdoor
```

**Key fields:**
- `Timestamp` — when it happened
- `Hostname` — which server
- `Service` — `sshd`, `sudo`, `useradd`, `PAM`
- `Action` — what happened (Accepted, Failed, Invalid user, etc.)
- `Source IP` — where the connection came from
- `Target user` — which account was targeted or used

---

## Investigation Exercises

### Exercise 1 — Find All Failed Logins

```bash
grep "Failed password" /var/log/auth.log

# Count failures by source IP
grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn

# Count failures by target user
grep "Failed password" /var/log/auth.log | awk '{print $9}' | sort | uniq -c | sort -rn
```

**What to look for:**
- Any single IP with more than 10 failures = brute force candidate
- Failures against `root` = common automated attack
- Failures against non-existent users (`Invalid user`) = username enumeration

---

### Exercise 2 — Find All Successful Logins

```bash
grep "Accepted" /var/log/auth.log

# Who logged in and from where
grep "Accepted" /var/log/auth.log | awk '{print $9, $11}'
```

**What to look for:**
- Logins from unexpected IPs (outside your known range)
- Logins at unusual times (3 AM on a Sunday)
- Logins to privileged accounts (root, service accounts)

---

### Exercise 3 — Review Sudo Usage

```bash
grep "sudo" /var/log/auth.log | grep -v "pam_unix"

# Who ran what as root
grep "COMMAND" /var/log/auth.log
```

**What to look for:**
- Commands that shouldn't be run (package installs, user additions, cron changes)
- Users who shouldn't have sudo running privileged commands
- Failed sudo attempts (`authentication failure`) = someone trying to escalate

---

### Exercise 4 — Check for New User Creation

```bash
grep "new user" /var/log/auth.log
grep "useradd" /var/log/auth.log
```

**What to look for:**
- Users created outside normal business hours
- Usernames that don't match your naming convention
- Users with UID 0 (root equivalents) — very suspicious

---

### Exercise 5 — Use `last` and `lastb`

```bash
# Successful logins history
last

# Failed login attempts (requires root)
sudo lastb | head -30

# Who is currently logged in
who
w
```

---

### Exercise 6 — Generate a Suspicious Scenario

Simulate an attack pattern and find it in the logs:

```bash
# From another VM: attempt SSH with wrong password 20 times
for i in {1..20}; do ssh wronguser@10.10.10.x 2>/dev/null; done

# Then on the server: find your simulation in the logs
grep "Failed password" /var/log/auth.log | tail -30
grep "Invalid user" /var/log/auth.log | tail -30
```

---

## Log Review Findings Template

```
=== LINUX AUTH LOG REVIEW ===
Date          : [date]
Host          : [hostname]
Analyst       : kristrbl
Log reviewed  : /var/log/auth.log
Time range    : [start] to [end]

SUMMARY OF FINDINGS
[brief description of what was notable]

FINDING 1 — [title]
Type          : [Brute Force / Unauthorized Login / Privilege Escalation / etc]
Source IP     : [IP]
Target User   : [username]
Count         : [number of events]
Time Window   : [start to end]
Log Evidence  :
  [paste relevant log lines]
Assessment    : [suspicious / expected / investigate further]

FINDING 2 — [repeat as needed]

NORMAL ACTIVITY CONFIRMED
- [list expected logins / sudo usage that was reviewed and confirmed benign]

RECOMMENDATIONS
- [any hardening steps worth noting]
```

---

## Hardening Notes

Document any findings that suggest configuration improvements:

| Issue Found | Recommendation |
|---|---|
| Root SSH login allowed | Disable in `/etc/ssh/sshd_config` → `PermitRootLogin no` |
| Password authentication enabled | Switch to key-based auth → `PasswordAuthentication no` |
| No account lockout | Install `fail2ban` |
| Excessive sudo users | Review `/etc/sudoers` — limit to required accounts only |

---

## Evidence to Collect

- [ ] Screenshot: `grep "Failed password"` output with count by IP
- [ ] Screenshot: `last` command output showing recent logins
- [ ] Screenshot: `lastb` output showing failed login history
- [ ] Screenshot: Suspicious log lines (brute force pattern visible)
- [ ] Screenshot: Sudo usage review
- [ ] Completed findings template (markdown file in this folder)

---

## Interview Talking Points

**"How do you investigate suspicious activity on a Linux server?"**
> I start with `/var/log/auth.log`. I grep for failed logins, group them by source IP to spot brute force patterns, and check for any successful logins from IPs outside the expected range. I also check sudo usage to see if any unexpected privilege escalation happened, and look for new user creation events. Then I cross-reference with `last` and `lastb` for the login history.

**"What command would you run to find brute force attempts on a Linux server?"**
> `grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn` — that gives me each source IP and how many times it failed, sorted by frequency. Any IP with a high count in a short window is worth investigating further.

---

## What This Proves to an Employer

- You can work with Linux command line tools for security investigation
- You know the structure of auth.log and can parse it meaningfully
- You can identify indicators of brute force, unauthorized access, and privilege escalation
- You understand Linux hardening basics
- You can produce a structured findings writeup from raw log data
