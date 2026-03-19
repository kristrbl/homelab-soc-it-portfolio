# Cost Analysis — Homelab vs Cloud

What this lab would cost to run in AWS/Azure vs the homelab.

## Homelab (One-Time Cost)

| Component | Est. Cost |
|-----------|-----------|
| PC (i7-6700K, 32GB RAM, storage) | ~$300-400 (used/built) |
| Electricity (~200W avg, 18hrs/day) | ~$15-25/month |
| **Total Year 1** | **~$500-700** |
| **Monthly after Year 1** | **~$20/month** |

## AWS Equivalent

| VM | Instance | Monthly Cost |
|----|----------|-------------|
| axiom-wazuh (4c/8GB) | t3.xlarge | $120 |
| axiom-ad (2c/4GB) | t3.medium | $30 |
| axiom-client (2c/4GB) | t3.medium | $30 |
| axiom-kali (2c/4GB) | t3.medium | $30 |
| axiom-tools (2c/4GB) | t3.medium | $30 |
| axiom-athena (4c/12GB) | t3.xlarge | $120 |
| axiom-splunk (4c/8GB) | t3.xlarge | $120 |
| axiom-linux (2c/2GB) | t3.small | $15 |
| axiom-client2 (2c/4GB) | t3.medium | $30 |
| axiom-netlab (2c/2GB) | t3.small | $15 |
| Storage (~800GB EBS) | gp3 | $64 |
| VPC + NAT Gateway | — | $35 |
| **Monthly Total** | | **~$639/month** |
| **Annual Total** | | **~$7,668/year** |

## Azure Equivalent

| Service | Monthly Cost |
|---------|-------------|
| 10 VMs (similar specs) | ~$550 |
| Storage (800GB managed) | ~$60 |
| Virtual Network + NSGs | ~$30 |
| **Monthly Total** | **~$640/month** |
| **Annual Total** | **~$7,680/year** |

## Comparison

| | Homelab | AWS | Azure |
|-|--------|-----|-------|
| Year 1 | ~$600 | ~$7,668 | ~$7,680 |
| Year 2+ | ~$240/yr | ~$7,668/yr | ~$7,680/yr |
| **3-Year Total** | **~$1,080** | **~$23,004** | **~$23,040** |
| Savings vs Cloud | — | **95%** | **95%** |

## Advantages of Each

### Homelab
- 95% cheaper over 3 years
- Full hardware control
- No egress charges
- Learn bare-metal skills (Proxmox, networking, storage)
- Always available (no billing surprises)

### Cloud
- No hardware maintenance
- Easy scaling
- Geographic redundancy
- Industry-standard for resume
- Better for team collaboration

## Conclusion

The homelab saves **~$22,000 over 3 years** while providing deeper learning opportunities. The skills transfer directly — Proxmox ≈ VMware/Hyper-V, iptables ≈ Security Groups/NSGs, LVM ≈ EBS.
