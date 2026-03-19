# Lab Roadmap — 20-Lab Build Order

## Dependencies

```
Lab 1: AD DC Setup
  └─► Lab 2: Domain Join + Users ──► Lab 3: GPOs
       └─► Lab 4: Wazuh Agent ──► Lab 5: Sysmon
            └─► Lab 7: Wazuh Multi-Agent
                 └─► Lab 11: Detection Labs
                      └─► Lab 17-18: Incident Response

Lab 6: Linux Admin (independent)
  └─► Lab 7: Wazuh Agent on Linux

Lab 8: GLPI Ticketing (independent)
  └─► Lab 9: Help Desk Scenarios

Lab 10: Splunk Install (independent)
  └─► Lab 11: Detection Engineering

Lab 13-14: Vulnerability Management (after Lab 7)
Lab 15-16: Network Labs (after Lab 6)
Lab 19: MITRE Mapping (after Lab 17-18)
Lab 20: Portfolio Polish (final)
```

## Phase Breakdown

### Phase 2 — Foundation (Labs 1-3)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 1 | AD DC Setup | axiom-ad | Static IP |
| 2 | Domain Join + Users/OUs/Groups | axiom-ad, axiom-client | Lab 1 |
| 3 | Group Policy Objects | axiom-ad, axiom-client | Lab 2 |

### Phase 3 — Monitoring (Labs 4-5)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 4 | Wazuh Verify + Agent Deploy | axiom-wazuh, axiom-client | Lab 2 |
| 5 | Sysmon on Windows | axiom-client | Lab 4 |

### Phase 4 — Linux & Multi-Agent (Labs 6-7)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 6 | Linux Administration | axiom-linux | OS install |
| 7 | Wazuh Multi-Agent | axiom-linux, axiom-tools | Lab 4, Lab 6 |

### Phase 5 — IT Operations (Labs 8-9)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 8 | GLPI Ticketing Setup | axiom-tools | Lab 7 |
| 9 | Help Desk Scenarios | axiom-tools, axiom-client | Lab 8 |

### Phase 6 — Splunk & Detection (Labs 10-12)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 10 | Splunk Install + Forwarding | axiom-splunk, axiom-client | OS install |
| 11 | Detection Rules (Sigma) | axiom-wazuh, axiom-splunk | Lab 10 |
| 12 | Advanced Detection | All SOC VMs | Lab 11 |

### Phase 7 — Vulnerability & Network (Labs 13-16)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 13 | OpenVAS Install | axiom-tools | Lab 7 |
| 14 | Vuln Scan + Remediate | axiom-tools, targets | Lab 13 |
| 15 | Network Diagnostics | axiom-netlab | Lab 6 |
| 16 | Packet Analysis | axiom-netlab, axiom-kali | Lab 15 |

### Phase 8 — Red Team & IR (Labs 17-20)
| Lab | Name | VMs | Prereqs |
|-----|------|-----|---------|
| 17 | Attack Simulation 1 | axiom-kali → corp | Lab 12 |
| 18 | Full IR Triage | All VMs | Lab 17 |
| 19 | MITRE ATT&CK Mapping | Documentation | Lab 18 |
| 20 | Portfolio Final Polish | GitHub | Lab 19 |
