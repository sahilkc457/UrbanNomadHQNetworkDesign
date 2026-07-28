<div align="center">

# 🏍️ Urban Nomad Motorcycle Gears
## Enterprise Network Design Project

*A complete, end-to-end enterprise network design for a mid-sized retail and distribution company — from business requirements through physical design, security architecture, WAN connectivity, and Cisco Packet Tracer simulation.*

---

![Cisco](https://img.shields.io/badge/Cisco-IOS%20%7C%20Catalyst%20%7C%20Meraki%20%7C%20Firepower-1BA0D7?logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer%208.2-1BA0D7?logo=cisco&logoColor=white)
![Security](https://img.shields.io/badge/Security-Zero%20Trust%20Architecture-E94560?logo=shield&logoColor=white)
![VPN](https://img.shields.io/badge/WAN-IPsec%20IKEv2%20Site--to--Site%20VPN-065F46?logoColor=white)
![Docs](https://img.shields.io/badge/Phases-7%20of%207%20Complete-166534?logoColor=white)
![Draw.io](https://img.shields.io/badge/Diagrams-draw.io-F08705?logoColor=white)

</div>

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Executive Summary](#-executive-summary)
- [Network at a Glance](#-network-at-a-glance)
- [Project Phases](#-project-phases)
- [IP Addressing Scheme](#-ip-addressing-scheme)
- [VLAN Design](#-vlan-design)
- [Network Topology](#-network-topology)
- [Security Architecture](#-security-architecture)
- [WAN & Branch Connectivity](#-wan--branch-connectivity)
- [Hardware Summary](#-hardware-summary)
- [Packet Tracer Simulation](#-packet-tracer-simulation)
- [Key Design Decisions](#-key-design-decisions)
- [Lessons Learned](#-lessons-learned)
- [Repository Structure](#-repository-structure)
- [Tools Used](#-tools-used)

---

## 🗂 Project Overview

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

## 📝 Executive Summary

Urban Nomad required a network capable of supporting:

- **Real-time ERP** synchronised across HQ and all 5 branches
- **VoIP telephony** across all departments and sites
- **Retail POS** terminals (PCI-DSS compliant, isolated)
- **E-commerce** web server (DMZ-hosted, internet-facing)
- **CCTV and biometric** access control (isolated IoT zone)
- **Guest WiFi** for retail customers (fully isolated)
- **Zero Trust security** — every device authenticated, every flow logged

The resulting design implements a **three-tier switching hierarchy**, **dual-ISP internet redundancy**, **Cisco ISE 802.1X NAC**, **Cisco Firepower NGFW with IDS/IPS**, **Wazuh SIEM**, and **IPsec IKEv2 site-to-site VPN** to all branches — all documented across 7 phases with supporting Packet Tracer simulation.

---

## 📊 Network at a Glance

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

## 🗺 Project Phases

The design is structured across 7 phases, each producing a standalone document:

| Phase | Document | Description |
|-------|----------|-------------|
| **[Phase 1](./phase1-BRD/)** | Business Requirements Document | Company profile, department mapping, critical systems, uptime requirements, security posture selection |
| **[Phase 2](./phase2-LND/)** | Logical Network Design | IP scheme, VLAN table, inter-VLAN ACL matrix, STP/MST design, trunk/access port standards, QoS DSCP policy, naming conventions |
| **[Phase 3](./phase3-PND/)** | Physical Network Design | Cisco hardware BoM (model numbers), MDF/IDF rack layouts, structured cabling (TIA-568), PoE budget, UPS sizing, WiFi site survey |
| **[Phase 4](./phase4-SEC/)** | Security & Edge Design | Firewall rule base, dual-ISP failover (IP SLA), Cisco ISE 802.1X NAC, Firepower IDS/IPS policy, IPsec VPN parameters, Wazuh SIEM, formal security policy |
| **[Phase 5](./phase5-WAN/)** | WAN & Branch Connectivity | Branch UNM-B01 full design, Meraki MX67 config, VPN bring-up procedure (31 steps), traffic flows, bandwidth sizing, 4G/LTE failover, all-5-branch comparison |
| **[Phase 6](./phase6-Lab/)** | Packet Tracer Lab Guide | 9 hands-on labs — VLANs, SVIs, DHCP, ACLs, NAT, VoIP (CME), site-to-site IPsec VPN — full IOS commands with explanations |
| **[Phase 7](./README.md)** | Master Documentation | This document — consolidated reference and GitHub portfolio |

---

## 🌐 IP Addressing Scheme

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

## 🔀 VLAN Design

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

## 🖧 Network Topology

### HQ Three-Tier Hierarchy

```
                        ┌─────────────────────────────────┐
                        │         INTERNET / WAN          │
                        │    ISP 1 (Primary)               │
                        │    ISP 2 (Failover)              │
                        └──────────────┬──────────────────┘
                                       │ Dual WAN
                        ┌──────────────▼──────────────────┐
                        │   HQ-FW-EDGE-01                 │
                        │   Cisco Firepower 1120          │  ← NGFW, IPS, VPN Hub
                        │   DMZ: 172.16.33.0/28           │
                        └──────────────┬──────────────────┘
                                       │ 10GbE Uplink
               ┌───────────────────────┴────────────────────────┐
               │            CORE LAYER (L3)                     │
               │  HQ-SW-CORE-01  ◄──StackWise──► HQ-SW-CORE-02 │
               │  Cisco Catalyst 9300-48P (×2)                  │
               │  SVIs for all VLANs | Inter-VLAN Routing       │
               └──┬──────────┬──────────┬──────────┬────────────┘
                  │          │          │           │  10GbE Fibre
          ┌───────▼──┐  ┌────▼───┐  ┌──▼─────┐  ┌─▼──────┐
          │ IDF DIST │  │Retail  │  │Warehse │  │Servers │   ← ACCESS LAYER
          │ 1st Floor│  │ACC SW  │  │ACC SW  │  │ACC SW  │
          │9200L-24P │  │9200-24P│  │9200-24T│  │9200-24P│
          └──┬──┬────┘  └───┬────┘  └───┬────┘  └────────┘
             │  │           │            │
         ACC-01 ACC-02    POS/Guest    CCTV/NVR
         Mgmt/HR IT/Sales  VLANs       VLANs

Legend:  ── = 1GbE  ══ = 10GbE  All inter-switch links are 802.1Q trunks
```

### WAN Hub-and-Spoke (HQ ↔ 5 Branches)

```
                    ┌──────────────────┐
                    │  HQ FIREPOWER    │
                    │  VPN Hub         │
                    │  Dual ISP        │
                    └──┬──┬──┬──┬──┬──┘
                       │  │  │  │  │  IPsec IKEv2 tunnels
           ┌───────────┘  │  │  │  └───────────┐
           │         ┌────┘  └────┐             │
           ▼         ▼            ▼             ▼
        B01-RT    B02-RT       B04-RT        B05-RT
        MX67      MX67         MX67          MX67
     Distribution Retail+Svc  Warehouse   Sales Office
                        ▲
                     B03-RT
                      MX67
                    Retail
```

---

## 🔒 Security Architecture

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

## 🌍 WAN & Branch Connectivity

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

## 🖥 Hardware Summary

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

## 🧪 Packet Tracer Simulation

The Phase 6 lab guide builds a functional simulation of the Urban Nomad HQ + Branch B01 in **Cisco Packet Tracer 8.2**:

| Lab | What's Configured | Verified By |
|-----|-------------------|-------------|
| Lab 1 | 20-device topology with labels | Canvas screenshot |
| Lab 2 | Core switch — 10 VLANs, 10 SVIs, 5 trunks, STP, SSH | `show vlan brief`, `show ip int brief` |
| Lab 3 | 4 access switches — trunk, access, voice VLAN, PortFast | `show interfaces trunk` |
| Lab 4 | DHCP for all VLANs on core switch (Option 150 for VoIP) | `show ip dhcp binding` |
| Lab 5 | PCs get DHCP, inter-VLAN pings confirmed | Ping matrix (7 tests) |
| Lab 6 | Guest, CCTV, POS ACLs applied to SVIs | `show ip access-lists` with hit counters |
| Lab 7 | Edge router NAT/PAT + internet cloud simulation | `show ip nat translations` |
| Lab 8 | Call Manager Express — 2 phones registered, call made | `show ephone registered` |
| Lab 9 | IPsec site-to-site VPN HQ ↔ B01, pings cross tunnel | `show crypto ipsec sa` |

> **Note:** PT uses IKEv1 for IPsec. Production design uses IKEv2 (documented in Phase 4). Concepts are identical — only the IKE negotiation phase syntax differs.

---

## 🧠 Key Design Decisions

### 1. Why 172.16.0.0/12?
172.16.0.0/12 provides over 1 million addresses across the `172.16.x.x – 172.31.x.x` range. Splitting it by third octet gives HQ, branches, and growth regions that are instantly identifiable in a `show ip route` output — far more operationally readable than 10.0.0.0/8 with less structure.

### 2. Why /23 subnets for VLANs?
/24 (254 hosts) is the instinct, but /23 (510 hosts) provides room to grow without a renumbering exercise — one of the most expensive and disruptive operations in network management. The cost is negligible (one extra bit), the benefit is significant.

### 3. Why Cisco ISE over Windows NPS?
NPS handles basic 802.1X but lacks device profiling, posture assessment, and granular policy sets. ISE enables dynamic VLAN assignment, guest onboarding, MAB for IoT, and CoA (Change of Authorisation) — allowing the NOC to push a device into a restricted VLAN in real-time without touching the switch. This is what enterprises actually deploy.

### 4. Why Meraki MX67 at branches instead of matching Firepower?
Zero-touch provisioning — a branch can go live with a courier delivery and a power cable. IT at HQ claims the device via dashboard before it ships. Meraki Auto VPN builds the tunnel automatically. For 10-person remote sites with no local IT, this operational simplicity outweighs the CLI control offered by Firepower. Costs are also significantly lower per-branch.

### 5. Why Wazuh over a commercial SIEM?
Wazuh is free, open-source, production-grade, and widely deployed in enterprise environments. It supports Syslog ingestion, file integrity monitoring, active response (auto-blocking via ISE CoA), and a full web dashboard. For a design project, it also demonstrates awareness of cost-conscious tooling decisions — something interviewers in SME and mid-market roles specifically value.

### 6. Why split-tunnel VPN for branches?
Without split tunnelling, all branch internet traffic hairpins through HQ — consuming expensive MPLS or VPN bandwidth and introducing latency. With split tunnelling, only HQ-bound traffic (ERP, File, VoIP) goes through the tunnel. General browsing exits the branch ISP locally. Exception: POS payment traffic is forced through the tunnel to HQ for centralised firewall inspection (PCI-DSS requirement).

---

## 💡 Lessons Learned

> *Reflections on what I would approach differently in a second iteration.*

**1. Start with a proper addressing plan before touching any device.**
The /12 split by region made routing and documentation clean throughout. In an earlier draft I started with 10.0.x.x and had to renumber when branch addressing collided with HQ management ranges. Plan the whole address space on paper first.

**2. VLAN count creep is real.**
I started with 5 VLANs (one per department) and ended with 11. Each new functional requirement (CCTV, POS, Guest, Voice, Management plane) justified a new segment. This is realistic — real networks always have more VLANs than the initial design assumes. The lesson: build your IP scheme with generous gaps from day one.

**3. The ACL matrix should be drawn before any configuration begins.**
Writing the 12×12 inter-VLAN matrix in Phase 2 forced me to think through every single traffic flow and justify it. This caught several security gaps — for example, I initially forgot that CCTV cameras would need a path to the NVR, and that the DMZ web server needs a restricted path back to the ERP server. The matrix is a design tool, not just documentation.

**4. NAT and VPN interact in ways that break things.**
In the Packet Tracer lab, VPN traffic was being NAT'd before entering the tunnel, causing the branch router to reject it (the source IP no longer matched the interesting traffic ACL). The fix — a deny statement in the NAT ACL to exempt VPN-bound traffic — is a real-world gotcha that trips up experienced engineers. Understanding it thoroughly was one of the most valuable parts of the lab.

**5. Document as you design, not after.**
By the time I reached Phase 7, every decision was already documented with its justification in the phase where it was made. Writing documentation retrospectively leads to incomplete justifications — you forget why you chose something. The phased approach enforced concurrent documentation.

---

## 📁 Repository Structure

```
urban-nomad-network-design/
│
├── README.md                          ← You are here (Phase 7)
│
├── phase1-BRD/
│   └── Urban_Nomad_Phase1_BRD.docx   ← Business Requirements Document
│
├── phase2-LND/
│   └── Urban_Nomad_Phase2_LND.docx   ← Logical Network Design
│
├── phase3-PND/
│   └── Urban_Nomad_Phase3_PND.docx   ← Physical Network Design
│
├── phase4-SEC/
│   └── Urban_Nomad_Phase4_SEC.docx   ← Security & Edge Design
│
├── phase5-WAN/
│   └── Urban_Nomad_Phase5_WAN.docx   ← WAN & Branch Connectivity
│
├── phase6-Lab/
│   └── Urban_Nomad_Phase6_PTLab.docx ← Packet Tracer Lab Guide
│
├── packet-tracer/
│   └── UrbanNomad_HQ_B01.pkt         ← Completed PT simulation file
│
└── screenshots/
    ├── SS-01_topology_canvas.png
    ├── SS-02_show_vlan_brief.png
    ├── SS-03_show_ip_int_brief.png
    ├── SS-04_show_interfaces_trunk.png
    ├── SS-05_pc_dhcp_assignment.png
    ├── SS-06_show_dhcp_binding.png
    ├── SS-07_intervlan_ping_pass.png
    ├── SS-08_guest_ping_blocked.png
    ├── SS-09_show_access_lists.png
    ├── SS-10_show_nat_translations.png
    ├── SS-11_ephone_registered.png
    ├── SS-12_voip_call_active.png
    ├── SS-13_isakmp_sa_qmidle.png
    └── SS-14_ipsec_sa_counters.png
```

---

## 🛠 Tools Used

| Tool | Purpose | Cost |
|------|---------|------|
| [Cisco Packet Tracer 8.2](https://netacad.com) | Network simulation — VLANs, routing, VoIP, VPN | Free (NetAcad account) |
| [draw.io / diagrams.net](https://diagrams.net) | Logical and physical topology diagrams | Free |
| [Microsoft Word / docx](https://microsoft.com) | Phase documents with professional formatting | Paid / Free online |
| [Wazuh](https://wazuh.com) | SIEM platform (documented in Phase 4) | Free (open-source) |
| [GitHub](https://github.com) | Version control and portfolio hosting | Free |

---

## 👤 About

This project was designed and documented as a portfolio piece demonstrating enterprise network engineering skills across:

- **Network design** — IP addressing, VLANs, routing, switching
- **Security architecture** — Zero Trust, firewalling, NAC, SIEM
- **WAN design** — VPN, redundancy, branch connectivity
- **Cisco IOS** — hands-on configuration via Packet Tracer
- **Documentation** — professional technical writing across all phases

> *Built using free tools. Designed to enterprise standards.*

---

<div align="center">

**Urban Nomad Motorcycle Gears — Enterprise Network Design Project**

*Phase 1 BRD → Phase 2 LND → Phase 3 PND → Phase 4 SEC → Phase 5 WAN → Phase 6 Lab → Phase 7 README*

</div>
