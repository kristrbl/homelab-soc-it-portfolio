# Windows Troubleshooting Lab

**Track:** IT Support | Help Desk | Desktop Support
**Status:** 📋 Planned

---

## Scenario

A series of common Windows 10/11 support tickets are simulated and resolved. Each scenario mirrors a real ticket a help desk technician would receive. The goal is to document a repeatable diagnostic process for each issue type: identify the symptom, isolate the cause, apply the fix, verify the resolution, and close the ticket with notes.

This lab is designed to show that you approach troubleshooting methodically, not randomly.

---

## Why This Matters for a Help Desk Role

Help desk and IT support roles are built on Windows troubleshooting. Every interview will include questions like "a user can't print," "a user can't connect to the internet," or "a user's computer is running slow." Being able to walk through your diagnostic process confidently — and having documented lab experience doing it — sets you apart from candidates who only know theory.

---

## Lab Environment

| Component | Details |
|---|---|
| Hypervisor | Proxmox VE |
| Endpoint | Windows 10 VM |
| Domain | `corp.local` (from AD lab) |
| Network | Internal subnet — 10.10.20.0/24 |

---

## Tools Used

- Windows 10
- Event Viewer
- Device Manager
- Task Manager / Resource Monitor
- `ipconfig`, `ping`, `tracert`, `nslookup`, `netstat`
- Services (`services.msc`)
- Registry Editor (`regedit`) — for specific scenarios
- PowerShell
- System File Checker (`sfc /scannow`)
- DISM

---

## Scenarios Covered

### Scenario 1 — User Cannot Connect to the Internet

**Symptom:** User reports no internet access. Can't open any websites.

**Diagnostic steps:**
```
1. ipconfig /all — check IP assignment (is it 169.x.x.x? APIPA = DHCP problem)
2. ping 8.8.8.8 — does layer 3 work? (yes = DNS problem, no = routing/NIC problem)
3. ping google.com — does DNS resolve? (fails = DNS issue)
4. ipconfig /release && ipconfig /renew — force new DHCP lease
5. netsh winsock reset — reset network stack if all else fails
6. Check Device Manager — NIC showing errors?
7. Check Event Viewer → Windows Logs → System — DHCP or NIC errors?
```

**Resolution:** Document which step isolated the issue and what was applied.

---

### Scenario 2 — Computer Running Slowly

**Symptom:** User says their computer has been slow all week.

**Diagnostic steps:**
```
1. Task Manager → CPU, RAM, Disk usage — what's maxed out?
2. Resource Monitor — which process is consuming the resource?
3. Startup tab in Task Manager — disable unnecessary startup programs
4. Check disk — right-click C: → Properties → Tools → Check
5. Check for Windows Updates — pending restarts cause slowdown
6. Event Viewer → Application/System — any recurring error events?
7. Run Disk Cleanup + Defrag (HDD only — never defrag SSD)
```

---

### Scenario 3 — User Profile Corrupted / Temporary Profile

**Symptom:** User logs in and gets a temporary profile. Their desktop and documents are missing.

**Diagnostic steps:**
```
1. Check Event Viewer → Application log → Filter for Event ID 1511 (temporary profile)
2. Navigate to C:\Users — is the user's profile folder there?
3. Check registry: HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList
   Look for a .bak suffix on the user's SID — this indicates a corrupted profile
4. Rename the .bak key to remove the suffix, restart
5. If profile is not recoverable: create new local profile, copy data from old profile
```

---

### Scenario 4 — Printer Not Working

**Symptom:** User cannot print. Printer shows as offline or jobs are stuck in queue.

**Diagnostic steps:**
```
1. Check physical connection (if local printer) — power, cable, status lights
2. Print queue — is there a stuck job? Clear the queue
3. Services → Print Spooler — is it running? If not, start it
   Net stop spooler → del /Q /F /S "%systemroot%\System32\spool\PRINTERS\*.*" → net start spooler
4. Device Manager — printer showing as error?
5. Can another user print? (isolates machine vs. shared printer issue)
6. Re-add printer — remove and reinstall the printer driver
7. For network printer: can you ping the printer IP? Is port 9100 open?
```

---

### Scenario 5 — Application Crash / Won't Launch

**Symptom:** User's application crashes on startup or throws an error.

**Diagnostic steps:**
```
1. Event Viewer → Windows Logs → Application → Filter for Error events near crash time
   Look for the application name and the error code
2. Check if the application is running as another user (Run as Administrator test)
3. Check if .NET or Visual C++ Redistributable is the dependency causing the error
4. Repair/reinstall the application
5. Check disk space — C: drive full will cause application failures
6. SFC /scannow — check for corrupt system files
```

---

### Scenario 6 — Blue Screen of Death (BSOD)

**Symptom:** User reports their computer blue screened and rebooted.

**Diagnostic steps:**
```
1. Get the stop code from the user (if they saw it) or from Event Viewer
   Event Viewer → Windows Logs → System → Filter: Event ID 41 (unexpected shutdown)
2. Check C:\Windows\Minidump — open dump file in WinDbg or upload to online analyzer
3. Common stop codes:
   DRIVER_IRQL_NOT_LESS_OR_EQUAL — driver issue (recent update/install?)
   MEMORY_MANAGEMENT — RAM issue (run Windows Memory Diagnostic)
   CRITICAL_PROCESS_DIED — system file corruption (sfc /scannow, DISM)
4. Check for recent Windows Updates or driver installs that correlate with the crash time
5. Roll back the driver or update if identified
```

---

## Evidence to Collect

- [ ] Screenshot: `ipconfig /all` showing APIPA or correct IP (Scenario 1)
- [ ] Screenshot: Task Manager with a high-CPU process identified (Scenario 2)
- [ ] Screenshot: Event Viewer showing Event ID 1511 temporary profile warning (Scenario 3)
- [ ] Screenshot: Print spooler stopped and restarted via Services (Scenario 4)
- [ ] Screenshot: Event Viewer application error with event ID and source (Scenario 5)
- [ ] Screenshot: Event ID 41 (unexpected shutdown) in System log (Scenario 6)
- [ ] Screenshot: `sfc /scannow` output

---

## Interview Talking Points

**"Walk me through how you troubleshoot a user who has no internet."**
> I start by running `ipconfig /all` to check if the machine has a valid IP. A 169.x address means DHCP failed. If the IP is valid, I ping the gateway to check local connectivity, then ping 8.8.8.8 to test routing. If that works but websites don't load, it's a DNS issue. From there I narrow it down — is it just their machine, or everyone on the segment? That tells me whether it's the endpoint or the network.

**"A user calls and says their computer is slow. What do you do?"**
> First I ask how long it's been slow and whether anything changed recently — new software, updates. Then I open Task Manager to see what's using CPU, RAM, or disk. If disk is at 100%, it's usually a Windows Update in progress or an antivirus scan. If RAM is maxed, I look at what processes are running and check startup programs. I document what I find before I change anything.

---

## What This Proves to an Employer

- You follow a structured diagnostic process, not random clicking
- You know the right tools for each type of Windows problem
- You document your findings and resolution steps
- You can translate a vague user complaint into a specific technical issue
- You've worked through the most common help desk ticket types hands-on
