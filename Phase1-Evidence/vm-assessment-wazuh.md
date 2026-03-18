# VM Assessment — axiom-wazuh (VMID 100)

**Date:** 2026-03-18
**Method:** Offline disk inspection from Proxmox host (mounted LVM read-only)

## Summary

| Property | Value |
|----------|-------|
| OS | Ubuntu 24.04.4 LTS (Noble Numbat) |
| Hostname | axiom-ops |
| User Account | kris (UID 1000) |
| Network | vmbr1 (SOC zone, 10.10.10.0/24) |
| Network Config | DHCP via Netplan on ens18 — **needs static IP** |
| Disk | 40 GB (LVM-on-LVM: ubuntu-vg/ubuntu-lv = 19 GB) |
| RAM | 8192 MB |
| Cores | 4 |

## Wazuh Status: INSTALLED (Full Stack)

All three Wazuh components are installed (v4.7.5):

| Component | Package | Status |
|-----------|---------|--------|
| Wazuh Manager | wazuh-manager 4.7.5-1 | Installed, enabled at boot |
| Wazuh Indexer | wazuh-indexer 4.7.5-1 | Installed, enabled at boot |
| Wazuh Dashboard | wazuh-dashboard 4.7.5-1 | Installed, enabled at boot |

### Key Wazuh Config (ossec.conf)
- Agent listener: TCP port 1514 (secure)
- Alert log level: 3+
- Rootcheck: enabled (files, trojans, dev, sys, pids, ports)
- Agent disconnect alert: 10 minutes
- Queue size: 131,072

### Certificates Present
- `/etc/wazuh-dashboard/certs/wazuh-dashboard.pem`
- `/etc/wazuh-indexer/certs/wazuh-indexer.pem`
- `/etc/filebeat/certs/wazuh-server.pem`

## Issues to Fix

1. **Network:** Currently set to DHCP — no DHCP server on vmbr1. Needs static IP (e.g., 10.10.10.10)
2. **Hostname:** Set to `axiom-ops`, should be `axiom-wazuh` for consistency
3. **No guest agent:** QEMU guest agent not responding (package may not be installed)

## Next Steps

- Boot VM, access via Proxmox console
- Set static IP: 10.10.10.10/24, gateway 10.10.10.1
- Rename hostname to axiom-wazuh
- Verify all three Wazuh services start cleanly
- Access Wazuh Dashboard (https://10.10.10.10:443)
