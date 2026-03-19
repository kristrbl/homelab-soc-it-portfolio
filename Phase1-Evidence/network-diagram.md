# Network Architecture Diagram

```
                          ┌─────────────────────────┐
                          │      INTERNET            │
                          │    (192.168.1.1 GW)      │
                          └────────────┬────────────┘
                                       │
                          ┌────────────┴────────────┐
                          │       vmbr0              │
                          │  Home / Isolated Zone    │
                          │    192.168.1.0/24        │
                          │                          │
                          │  ┌─────────────────────┐ │
                          │  │ axiom-core (host)    │ │
                          │  │ 192.168.1.50         │ │
                          │  │ Proxmox VE 9.1.6     │ │
                          │  │ i7-6700K | 32GB RAM  │ │
                          │  └─────────────────────┘ │
                          │                          │
                          │  axiom-athena (105)      │
                          │  axiom-forge  (106)      │
                          │  athena-shield(108)      │
                          └──────────┬───────────────┘
                                     │
                    ┌────────────────┼────────────────┐
                    │                │                 │
         ┌──────────┴──────────┐    │    ┌────────────┴───────────┐
         │       vmbr1         │    │    │        vmbr2           │
         │  SOC / Management   │    │    │     Corporate          │
         │   10.10.10.0/24     │    │    │    10.10.20.0/24       │
         │                     │    │    │                        │
         │  axiom-wazuh (100)  │    │    │  axiom-ad     (101)    │
         │  axiom-tools (104)  │    │    │  axiom-client (102)    │
         │  axiom-splunk(109)  │    │    │  axiom-client2(111)    │
         │                     │    │    │  axiom-linux  (110)    │
         │  GW: 10.10.10.1    │    │    │  axiom-netlab (107)    │
         └──────────┬──────────┘    │    │                        │
                    │               │    │  GW: 10.10.20.1       │
                    │               │    └────────────┬───────────┘
                    │               │                 │
                    │    ┌──────────┴──────────┐      │
                    │    │       vmbr3         │      │
                    │    │   Attack Zone       │      │
                    │    │  10.10.30.0/24      │      │
                    │    │                     │      │
                    │    │  axiom-kali (103)   │      │
                    │    │                     │      │
                    │    │  GW: 10.10.30.1    │      │
                    │    └──────────┬──────────┘      │
                    │               │                 │
                    │               │                 │
         ┌──────────┴───────────────┴─────────────────┴──────────┐
         │                    FIREWALL POLICY                     │
         │                  (iptables on host)                    │
         │                                                        │
         │  Default: FORWARD DROP                                 │
         │                                                        │
         │  SOC (vmbr1) ──────────► ALL zones         [ALLOW]    │
         │  Corporate (vmbr2) ────► SOC only           [ALLOW]    │
         │  Attack (vmbr3) ───────► Corporate only     [ALLOW]    │
         │  Attack (vmbr3) ───────► SOC                [DENY+LOG] │
         │  Corporate (vmbr2) ────► Attack             [DENY]     │
         │                                                        │
         │  NAT: All lab zones masquerade via vmbr0 for internet  │
         └────────────────────────────────────────────────────────┘
```

## Traffic Flow Logic

| Source | Destination | Allowed | Reason |
|--------|-------------|---------|--------|
| SOC (10.10.10.0/24) | Any zone | Yes | Wazuh pulls logs, tools scan all targets |
| Corporate (10.10.20.0/24) | SOC (10.10.10.0/24) | Yes | Agents send logs to Wazuh (1514/1515) |
| Corporate (10.10.20.0/24) | Attack (10.10.30.0/24) | **No** | Prevent lateral movement to attacker infra |
| Attack (10.10.30.0/24) | Corporate (10.10.20.0/24) | Yes | Red team attacks corporate targets |
| Attack (10.10.30.0/24) | SOC (10.10.10.0/24) | **No + Logged** | Protect SIEM evidence integrity |
| Any lab zone | Internet (via vmbr0) | Yes | NAT masquerade for updates/packages |
