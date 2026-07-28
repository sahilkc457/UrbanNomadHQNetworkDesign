<div align="center">

# Urban Nomad Motorcycle Gears - Enterprise Network Design Documentation

*A complete, end-to-end enterprise network design for a mid-sized retail and distribution company — from business requirements through physical design, security architecture, WAN connectivity, and Cisco Packet Tracer simulation.*

---

![Cisco](https://img.shields.io/badge/Cisco-IOS%20%7C%20Catalyst%20%7C%20Meraki%20%7C%20Firepower-1BA0D7?logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer%208.2-1BA0D7?logo=cisco&logoColor=white)
![Security](https://img.shields.io/badge/Security-Zero%20Trust%20Architecture-E94560?logo=shield&logoColor=white)
![VPN](https://img.shields.io/badge/WAN-IPsec%20IKEv2%20Site--to--Site%20VPN-065F46?logoColor=white)

</div>


## Project Overview

This project is a **full enterprise network design** for *Urban Nomad Motorcycle Gears* — a fictional mid-sized retail and distribution company used as the basis for a realistic, interview-ready portfolio.

The design covers **one headquarters** (80–120 staff, 2-floor building) and **five remote branch sites** (10 staff each), addressing every layer of the network stack from structured cabling through to security policy and WAN encryption.

| Attribute | Detail |
|-----------|--------|
| **Company** | Urban Nomad Motorcycle Gears |
| **Industry** | Retail & Distribution — Motorcycle Gear |
| **HQ Staff** | 80–120 |
| **Branch Sites** | 5 remote sites × ~10 staff |
| **Total Users** | ~170 across all sites |
| **Departments** | Management, HR, IT/NOC, Warehouse, Sales |
| **Business Operations** | Retail storefront, e-commerce, remote distribution |
| **Uptime Requirement** | Critical — downtime = direct revenue loss |

---

## Executive Summary

Urban Nomad required a network capable of supporting:

- **Real-time ERP** synchronised across HQ and all 5 branches
- **VoIP telephony** across all departments and sites
- **Retail POS** terminals (PCI-DSS compliant, isolated)
- **E-commerce** web server (DMZ-hosted, internet-facing)
- **CCTV and biometric** access control (isolated IoT zone)
- **Guest WiFi** for retail customers (fully isolated)
- **Zero Trust security** — every device authenticated, every flow logged

The resulting design implements a **three-tier switching hierarchy**, **dual-ISP internet redundancy**, **Cisco ISE 802.1X NAC**, **Cisco Firepower NGFW with IDS/IPS**, **Wazuh SIEM**, and **IPsec IKEv2 site-to-site VPN** to all branches.
---
## Network Topology

### HQ Three-Tier Physcial Design

![Network Topology](screenshots/physicaldesign.png)



### WAN Hub-and-Spoke (HQ ↔ 5 Branches)

//In progress
---

## Network at a Glance

| Metric | Value |
|--------|-------|
| **Address Space** | 172.16.0.0/12 (private) |
| **VLANs Designed** | 11 (5 department + 6 functional) |
| **Subnets** | /23 per VLAN (510 usable hosts each) |
| **DMZ** | 172.16.33.0/28 (13 hosts — tightly scoped) |
| **Branch Address Block** | 172.17.0.0/16 (/20 per branch) |
| **Internet Redundancy** | Dual ISP + IP SLA failover (< 30s) |
| **Branch WAN** | IPsec IKEv2 VPN + 4G/LTE failover |
| **Wireless** | WiFi 6 (802.11ax) — 3 SSIDs |
| **Core Switching** | Cisco Catalyst 9300 redundant pair (StackWise) |
| **Firewall** | Cisco Firepower 1120 (Snort IPS, SSL inspection) |
| **AAA/NAC** | Cisco ISE 3.x — 802.1X EAP-TLS per port |
| **SIEM** | Wazuh (open-source) + SNMPv3 monitoring |
| **VoIP** | Cisco CUCM — G.711 (LAN) / G.729 (WAN) |

---


## IP Addressing Scheme

### Address Space: `172.16.0.0/12`

| Region | Block | Range | Purpose |
|--------|-------|-------|---------|
| HQ Department VLANs | 172.16.0.0/20 | .0.0 – .15.255 | 5 department /23 subnets |
| HQ Functional VLANs | 172.16.16.0/20 | .16.0 – .31.255 | Voice, Servers, CCTV, POS, Guest |
| HQ Network Management | 172.16.32.0/24 | .32.0 – .32.255 | OOB device management IPs |
| HQ DMZ | 172.16.33.0/28 | .33.0 – .33.15 | E-commerce web server (13 hosts) |
| Branch Sites (×5) | 172.17.0.0/16 | .17.0.0 – .17.255.255 | /20 per branch, /24 per VLAN |
| Reserved / Growth | 172.18.0.0/15 | .18.0.0 – .19.255.255 | Future expansion — unassigned |

---

## VLAN Design

### Department VLANs

| VLAN ID | Name | Subnet | Gateway | Segment | Sensitivity |
|---------|------|--------|---------|---------|-------------|
| 10 | VLAN-MGMT-EXEC | 172.16.0.0/23 | 172.16.0.1 | Management & Executive | 🔴 High |
| 20 | VLAN-HR-ADMIN | 172.16.2.0/23 | 172.16.2.1 | HR & Administration | 🔴 High (PII) |
| 30 | VLAN-IT-NOC | 172.16.4.0/23 | 172.16.4.1 | IT & Network Ops | 🔴 Critical |
| 40 | VLAN-WAREHOUSE | 172.16.6.0/23 | 172.16.6.1 | Warehouse & Logistics | 🟡 Medium |
| 50 | VLAN-SALES-CS | 172.16.8.0/23 | 172.16.8.1 | Sales & Customer Service | 🟡 Medium |

### Functional VLANs

| VLAN ID | Name | Subnet | Gateway | Purpose | QoS Priority |
|---------|------|--------|---------|---------|--------------|
| 60 | VLAN-VOICE | 172.16.16.0/23 | 172.16.16.1 | VoIP — all IP phones | DSCP EF (46) — Strict |
| 70 | VLAN-SERVERS | 172.16.18.0/23 | 172.16.18.1 | AD, ERP, File, NVR, VoIP CM | DSCP AF41 |
| 80 | VLAN-CCTV | 172.16.20.0/23 | 172.16.20.1 | IP cameras + biometrics | DSCP AF11 — Rate-limited |
| 90 | VLAN-POS | 172.16.22.0/23 | 172.16.22.1 | POS terminals (PCI-DSS) | DSCP AF41 |
| 100 | VLAN-GUEST | 172.16.24.0/23 | 172.16.24.1 | Customer WiFi — isolated | DSCP CS1 — Scavenger |
| 999 | VLAN-NATIVE | N/A | N/A | Blackhole — unused ports | Blocked |

---



## Security Architecture

### Defence-in-Depth Model

| Layer | Controls |
|-------|----------|
| **Perimeter** | Dual-ISP failover, Cisco Firepower 1120 NGFW, DMZ isolation, IP reputation filtering |
| **Network** | VLAN segmentation, inter-VLAN ACLs (12×12 matrix), 802.1X port auth, storm control |
| **Identity** | Cisco ISE 3.x, Active Directory, EAP-TLS certificates, MFA for VPN |
| **Application** | Snort IPS (hybrid mode), SSL/TLS inspection, WAF for e-commerce, DNS filtering |
| **Data** | File server RBAC, encrypted backups, PCI-DSS POS isolation |
| **Monitoring** | Wazuh SIEM, Syslog centralisation, SNMPv3, 90-day minimum log retention |

### Zero Trust Principles Applied

- **Verify Explicitly** — 802.1X on every wired port; EAP-TLS for corporate devices, MAB for IoT/POS
- **Least Privilege** — RBAC per department VLAN; IT is the only VLAN with cross-VLAN access
- **Assume Breach** — inter-VLAN ACLs deny by default; CCTV, POS, and Guest are fully isolated
- **Continuous Monitoring** — every firewall hit, auth event, and VPN session logged to Wazuh

### Key Firewall Rules (Summary)

| Direction | Rule | Action |
|-----------|------|--------|
| Internet → DMZ | HTTPS/HTTP to web server only | PERMIT + inspect |
| Internal → Internet | All VLANs (department-specific ports) | PERMIT + SSL inspect |
| Guest → Internal | Any destination inside 172.16.x.x | **DENY** |
| POS → Internal | Any internal VLAN | **DENY** (payment GW only via FW) |
| CCTV → Internet | Any outbound | **DENY** |
| Branch VPN → HQ | ERP, File Server, Call Manager only | PERMIT (specific) |
| Management (32) | Non-IT VLANs | **DENY** — IT only, SSH only |

---

## WAN & Branch Connectivity

### VPN Architecture

| Parameter | Value |
|-----------|-------|
| **Protocol** | IPsec IKEv2 |
| **Encryption** | AES-256-GCM |
| **Integrity** | SHA-384 |
| **DH Group** | Group 20 (384-bit ECC) |
| **PFS** | Enabled |
| **Topology** | Hub-and-spoke — HQ is the hub |
| **Branch Device** | Cisco Meraki MX67 (cloud-managed) |
| **Primary Tunnel** | Branch fixed-line ISP → HQ ISP 1 |
| **Failover Tunnel** | Branch 4G/LTE → HQ ISP 2 (auto, ~30s) |
| **Split Tunnel** | Enabled — internet exits branch locally |

### Branch Sites

| Site | Code | Function | Key Services via VPN |
|------|------|----------|----------------------|
| Branch 1 | UNM-B01 | Distribution Centre | ERP, File Server, VoIP |
| Branch 2 | UNM-B02 | Retail + Service | ERP, POS (FW-inspected), VoIP |
| Branch 3 | UNM-B03 | Retail Storefront | POS (FW-inspected), VoIP |
| Branch 4 | UNM-B04 | Warehouse | ERP, VoIP |
| Branch 5 | UNM-B05 | Sales Office | File Server, VoIP |

---

## Hardware Summary

### HQ Core Infrastructure

| Device | Model | Qty | Role |
|--------|-------|-----|------|
| Core Switch | Cisco Catalyst 9300-48P | 2 | L3 core, SVIs, StackWise redundancy |
| Distribution Switch | Cisco Catalyst 9200L-24P | 1 | 1st floor IDF aggregation |
| Access Switches | Cisco Catalyst 9200L (24/48P) | 4 | Department access, PoE+ |
| Perimeter Firewall | Cisco Firepower 1120 | 1 | NGFW, dual-ISP, VPN hub, IPS |
| Wireless Controller | Cisco Catalyst 9800-CL | 1 | Central WiFi management |
| Access Points | Cisco Catalyst 9130AXI (WiFi 6) | 9 | Office (6) + Retail/Warehouse (3) |
| Domain Controller | Dell PowerEdge R450 | 1 | AD, DNS, DHCP |
| ERP Server | HPE ProLiant DL360 | 1 | ERP application + database |
| File & Print Server | Dell PowerEdge R450 | 1 | Shared drives, print management |
| Call Manager | Cisco CUCM (VM) | 1 | VoIP — all sites |
| NVR | Synology RS1221+ | 1 | CCTV footage storage (RAID6, 20TB) |
| UPS | APC Smart-UPS SRT 3000VA | 2 | Server room (N+1 redundancy) |

### Branch (Per Site — UNM-B01 Representative)

| Device | Model | Role |
|--------|-------|------|
| Branch Edge/VPN | Cisco Meraki MX67 | WAN, VPN, firewall, 4G failover |
| Access Switch | Cisco Catalyst 9200L-24P-4X | PoE+ for phones + AP |
| Access Point | Cisco Catalyst 9130AXI | WiFi 6 — Corporate + Guest |
| IP Phones | Cisco IP Phone 8851 | VoIP — registers to HQ CUCM via VPN |
| IP Cameras | Cisco Meraki MV12 | CCTV — PoE, local NVR + HQ sync |

---



## About

This project was designed and documented as a portfolio piece demonstrating enterprise network engineering skills across:

- **Network design** — IP addressing, VLANs, routing, switching
- **Security architecture** — Zero Trust, firewalling, NAC, SIEM
- **WAN design** — VPN, redundancy, branch connectivity
- **Cisco IOS** — hands-on configuration via Packet Tracer
- **Documentation** — professional technical writing across all phases

> *Built using free tools. Designed to enterprise standards.*

---

<div align="center">

**Urban Nomad Motorcycle Gears - Enterprise Network Design Project**

</div>
