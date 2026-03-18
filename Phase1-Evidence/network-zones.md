# Network Zone Configuration — axiom-core

Four isolated network zones with IP forwarding enabled on the SOC bridge.

## vmbr0 — Home / Isolated (192.168.1.0/24)
```
auto vmbr0
iface vmbr0 inet static
    address 192.168.1.50/24
    gateway 192.168.1.1
    bridge-ports nic0
```
**VMs:** axiom-athena, axiom-forge, athena-shield
**Purpose:** Internet-facing, personal/isolated VMs not part of lab domain

## vmbr1 — SOC / Management (10.10.10.0/24)
```
auto vmbr1
iface vmbr1 inet static
    address 10.10.10.1/24
    bridge-ports none
    post-up echo 1 > /proc/sys/net/ipv4/ip_forward
```
**VMs:** axiom-wazuh, axiom-tools, axiom-splunk
**Purpose:** SIEM, monitoring, admin tools, vulnerability scanning

## vmbr2 — Corporate (10.10.20.0/24)
```
auto vmbr2
iface vmbr2 inet static
    address 10.10.20.1/24
    bridge-ports none
```
**VMs:** axiom-ad, axiom-client, axiom-client2, axiom-linux, axiom-netlab
**Purpose:** Domain controller, workstations, servers — simulated enterprise network

## vmbr3 — Attack (10.10.30.0/24)
```
auto vmbr3
iface vmbr3 inet static
    address 10.10.30.1/24
    bridge-ports none
```
**VMs:** axiom-kali
**Purpose:** Isolated red team testing only — controlled attack simulations
