# MITRE ATT&CK Coverage Map

Planned detection coverage across the 20-lab build, mapped to MITRE ATT&CK techniques.

## Tactics & Techniques Covered

### Initial Access (TA0001)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Phishing simulation | T1566 | Wazuh email rules | Lab 17 |
| Valid Accounts | T1078 | AD audit logs, Wazuh | Lab 4, 11 |

### Execution (TA0002)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| PowerShell | T1059.001 | Sysmon EventID 1, ScriptBlock logging | Lab 5 |
| Command-Line Interface | T1059 | Sysmon process creation | Lab 5 |
| Scheduled Task | T1053.005 | Sysmon EventID 1, Windows Event 4698 | Lab 12 |

### Persistence (TA0003)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Registry Run Keys | T1547.001 | Sysmon EventID 13 | Lab 12 |
| New Service | T1543.003 | Windows Event 7045, Sysmon | Lab 12 |
| Account Creation | T1136 | Windows Event 4720, Wazuh | Lab 11 |

### Privilege Escalation (TA0004)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Group Policy Modification | T1484.001 | Windows Event 5136, Wazuh | Lab 3 |
| Token Impersonation | T1134 | Sysmon, Wazuh rules | Lab 18 |

### Defense Evasion (TA0005)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Log Clearing | T1070.001 | Windows Event 1102, Wazuh | Lab 11 |
| Disable Security Tools | T1562 | Sysmon, Wazuh agent disconnect | Lab 12 |

### Credential Access (TA0006)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Brute Force | T1110 | Windows Event 4625, Wazuh | Lab 11, 17 |
| Kerberoasting | T1558.003 | Windows Event 4769, Splunk | Lab 18 |

### Discovery (TA0007)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Network Scanning | T1046 | Suricata, Wazuh | Lab 17 |
| Account Discovery | T1087 | Sysmon, AD audit logs | Lab 12 |

### Lateral Movement (TA0008)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Remote Services (RDP) | T1021.001 | Windows Event 4624 Type 10, Wazuh | Lab 18 |
| Pass the Hash | T1550.002 | Windows Event 4624 Type 9, Splunk | Lab 18 |

### Collection (TA0009)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Data from Local System | T1005 | Sysmon file access monitoring | Lab 18 |

### Exfiltration (TA0010)
| Technique | ID | Detection Source | Lab |
|-----------|----|-----------------|-----|
| Exfil over C2 | T1041 | Suricata, network monitoring | Lab 18 |

## Coverage Summary
- **10 Tactics** covered across all labs
- **20+ Techniques** with specific detection rules
- **3 Detection Engines**: Wazuh, Splunk, Suricata
- **2 Endpoint Sources**: Sysmon, Windows Event Logs
