## VLAN Design

### Department VLANs

| VLAN ID | Name | Subnet | Gateway | Segment | Sensitivity |
|---------|------|--------|---------|---------|-------------|
| 10 | VLAN-MGMT-EXEC | 172.16.0.0/23 | 172.16.0.1 | Management & Executive | 🔴 High |
| 20 | VLAN-HR-ADMIN | 172.16.2.0/23 | 172.16.2.1 | HR & Administration | 🔴 High (PII) |
| 30 | VLAN-IT-NOC | 172.16.4.0/23 | 172.16.4.1 | IT & Network Ops | 🔴 Critical |
| 40 | VLAN-WAREHOUSE | 172.16.6.0/23 | 172.16.6.1 | Warehouse & Logistics | 🟡 Medium |
| 50 | VLAN-SALES-CS | 172.16.8.0/23 | 172.16.8.1 | Sales & Customer Service | 🟡 Medium |
---
### Functional VLANs

| VLAN ID | Name | Subnet | Gateway | Purpose | QoS Priority |
|---------|------|--------|---------|---------|--------------|
| 60 | VLAN-VOICE | 172.16.16.0/23 | 172.16.16.1 | VoIP — all IP phones | DSCP EF (46) — Strict |
| 70 | VLAN-SERVERS | 172.16.18.0/23 | 172.16.18.1 | AD, ERP, File, NVR, VoIP CM | DSCP AF41 |
| 80 | VLAN-CCTV | 172.16.20.0/23 | 172.16.20.1 | IP cameras + biometrics | DSCP AF11 — Rate-limited |
| 90 | VLAN-POS | 172.16.22.0/23 | 172.16.22.1 | POS terminals (PCI-DSS) | DSCP AF41 |
| 100 | VLAN-GUEST | 172.16.24.0/23 | 172.16.24.1 | Customer WiFi — isolated | DSCP CS1 — Scavenger |
| 999 | VLAN-NATIVE | N/A | N/A | Blackhole — unused ports | Blocked |

