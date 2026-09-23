# 🏦 Banking Network Security Project — ENSP Documentation

## 📋 Project Overview

This is a **Huawei ENSP (Enterprise Network Simulator)** project simulating a **complete banking network infrastructure** with security policies, NAT, firewall filtering, and multi-zone architecture. The project demonstrates network security concepts including:

- Network segmentation with security zones
- Firewall security policies (permit/deny rules)
- NAT (Network Address Translation) — both Static NAT Server and Source NAT (Easy-IP)
- ASPF (Application Layer Packet Filter) for FTP control
- Attack defense (ICMP flood, SYN flood)
- AAA and management security
- Full packet verification and evidence capture

---

## 📁 Project Structure

```
G:\nti\+final_ensp\projact\
│
├── ☁️ cloud/
│   └── topology_cloud.json          ← ENSP Cloud topology configuration (JSON)
│
├── ⚙️ configration/
│   ├── FW-Bank.txt                  ← Huawei Firewall (USG6525E) full configuration
│   ├── Server.txt                   ← Server IP/Mask/Gateway/Service table
│   ├── PCs.txt                      ← PC IP/Mask/Gateway/Zone table
│   ├── R-Internet.txt               ← R-Internet Router configuration
│   ├── R-Attacker.txt               ← R-Attacker Router configuration
│   ├── R-BackOffice.txt             ← R-BackOffice Router configuration
│   └── Verification.txt             ← Verification/test commands
│
├── 🖼️ photo/
│   ├── info.txt                     ← Detailed photo descriptions & verification notes
│   ├── interface ensp.png           ← ENSP interface screenshot
│   ├── interface cloud.jpeg         ← Cloud interface screenshot
│   ├── topolgy ensp.png             ← ENSP topology diagram
│   ├── topolgy cloud .jpeg          ← Cloud topology diagram
│   ├── NAT policy cloud.jpeg        ← NAT policy configuration screenshot
│   ├── NAT policy ensp.png          ← NAT policy in ENSP
│   ├── policy security ensp.png     ← Security policy in ENSP
│   ├── security policy cloud .jpeg  ← Security policy cloud view
│   ├── Zone ensp.png                ← Zone configuration screenshot
│   ├── Zone list cloud.jpeg         ← Zone list in cloud view
│   └── 1.png, 2.png, 3.png, 4.png, 5.png, 6.png ← Verification evidence screenshots
│
├── 🎬 vidoes/
│   ├── New folder/                  ← Video files directory
│   └── New folder.zip               ← Compressed video archive
│
├── 📽️ presention/
│   ├── presentation.pptx            ← PowerPoint presentation
│   └── presntation.pdf              ← PDF presentation
│
├── 📂 projact/                      ← Duplicate of project files (backup)
│   ├── configration/
│   ├── photo/
│   ├── presentation.pptx
│   ├── presntation.pdf
│   └── vidoes/
│
└── 🔬 ensp projact/                 ← ENSP project workspace (15 .topo files + topology)
    ├── *.topo                       ← 15 ENSP topology save files
    └── topo.topo                    ← Main ENSP topology file
```

---

## 🏗️ Network Topology

### 📊 Logical Diagram

```
                        ┌─────────────────────────────────────────────┐
                        │              INTERNET (Cloud1)              │
                        │         R-Internet (8.8.8.8 / LoopBack)     │
                        │         IP: 200.1.1.100 / 100.1.1.1         │
                        └──────────────────┬──────────────────────────┘
                                           │ GigE0/0/3 (200.1.1.100)
                                           │
                        ┌──────────────────▼──────────────────────────┐
                        │           FW-FW (USG6525E)                  │
                        │        IP: 200.1.1.1 (GigE1/0/0)           │
                        │                                             │
                        │  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ │
                        │  │Teller│ │BackOff│ │ ATM │ │CustW│ │DMZ  │ │
                        │  │ZONE  │ │ZONE   │ │ZONE │ │WIFI │ │ZONE │ │
                        │  │192.16│ │192.16 │ │192.1│ │192.1│ │192.1│ │
                        │  │8.10. │ │8.20.  │ │8.30 │ │8.40 │ │8.50 │ │
                        │  │1.1   │ │1.1    │ │1.1  │ │1.1  │ │1.1  │ │
                        │  └──┬───┘ └──┬────┘ └──┬───┘ └──┬───┘ └──┬──┘ │
                        │     │        │         │        │        │    │
                        └─────┼────────┼─────────┼────────┼────────┼────┘
                              │        │         │        │        │
                    ┌─────────▼──┐  ┌──▼─────┐ ┌─▼────┐ ┌▼─────┐ ┌▼─────────┐
                    │  R-Attacker │  │SRV-DB │ │SRV-  │ │SRV-  │ │  CCTV    │
                    │  (100.2.2.1)│  │192.168│ │WEB   │ │FTP   │ │192.168.60│
                    └────────────┘  │50.11  │ │192.1 │ │192.1 │ │10        │
                                  │       │ │50.10 │ │50.12 │ │          │
                                  └───────┘ └──────┘ └──────┘ └──────────┘
```

### 📍 Device List

| Device | Type | IP Address | Zone/Role |
|--------|------|------------|-----------|
| R-Internet | Router | 200.1.1.100 / 100.1.1.1 / LoopBack: 8.8.8.8 | Internet Gateway |
| R-Attacker | Router | 200.1.1.200 / 100.2.2.1 | Attacker/Test PC |
| R-BackOffice | Router | 192.168.20.10 | BackOffice Gateway |
| FW-FW | Firewall (USG6525E) | 200.1.1.1 (untrust), 192.168.0.1 (local) | Core Firewall |
| SW1-SW5 | Switch | — | Internal switching |
| SRV-WEB | Server | 192.168.50.10 | Web Banking Server |
| SRV-DB | Server | 192.168.50.11 | Database Server (Oracle 1521) |
| SRV-FTP | Server | 192.168.50.12 | FTP Server |
| PC1 | PC | 100.1.1.10 | Behind R-Internet |
| PC2 | PC | 100.2.2.10 | Behind R-Attacker |
| PC3 | PC | 192.168.40.10 | Customer WiFi (Z-CUSTWIFI) |
| PC4 | PC | 192.168.30.10 | ATM (Z-ATM) |
| PC5 | PC | 192.168.60.10 | CCTV (Z-CCTV) |
| PC6 | PC | 192.168.20.10 | BackOffice (Z-BACKOFFICE) |
| PC7 | PC | 192.168.10.10 | Teller (Z-TELLER) |

---

## 🔥 Firewall Configuration (USG6525E)

### Interfaces & Zones

| Interface | IP Address | Zone | Priority |
|-----------|-----------|------|----------|
| GigE0/0/0 | 192.168.0.1/24 | local | 100 |
| GigE1/0/0 | 200.1.1.1/24 | untrust | 5 |
| GigE1/0/1 | 192.168.10.1/24 | Z-TELLER | 84 |
| GigE1/0/2 | 192.168.20.1/24 | Z-BACKOFFICE | 80 |
| GigE1/0/3 | 192.168.30.1/24 | Z-ATM | 60 |
| GigE1/0/4 | 192.168.40.1/24 | Z-CUSTWIFI | 10 |
| GigE1/0/5 | 192.168.50.1/24 | Z-DMZ | 49 |
| GigE1/0/6 | 192.168.60.1/24 | Z-CCTV | 20 |

### Security Policy Rules (17 Rules)

| # | Rule Name | Source Zone | Destination Zone | Action | Service | Description |
|---|-----------|------------|-----------------|--------|---------|-------------|
| 1 | `inbound_webbank` | untrust | Z-DMZ | **permit** | http, https | Internet → Web Banking |
| 2 | `deny_internet_other` | untrust | — | **deny** | all | Default deny from Internet |
| 3 | `teller_to_db` | Z-TELLER | Z-DMZ | **permit** | DB_1521 (1521/tcp) | Teller → Database |
| 4 | `deny_teller_internet` | Z-TELLER | untrust | **deny** | all | Teller cannot access Internet |
| 5 | `deny_teller_other` | Z-TELLER | — | **deny** | all | Teller deny other |
| 6 | `bo_to_web` | Z-BACKOFFICE | Z-DMZ | **permit** | http, https | BackOffice → Web |
| 7 | `bo_to_db` | Z-BACKOFFICE | Z-DMZ | **permit** | DB_1521 | BackOffice → Database |
| 8 | `bo_to_ftp` | Z-BACKOFFICE | Z-DMZ | **permit** | ftp + ASPF | BackOffice → FTP |
| 9 | `bo_to_internet` | Z-BACKOFFICE | untrust | **permit** | all | BackOffice → Internet |
| 10 | `bo_to_cctv` | Z-BACKOFFICE | Z-CCTV | **permit** | http, rtsp | BackOffice → CCTV |
| 11 | `atm_to_db` | Z-ATM | Z-DMZ | **permit** | DB_1521 | ATM → Database |
| 12 | `deny_atm_other` | Z-ATM | — | **deny** | all | ATM deny other |
| 13 | `custwifi_to_internet` | Z-CUSTWIFI | untrust | **permit** | all | Customer WiFi → Internet |
| 14 | `deny_custwifi_internal` | Z-CUSTWIFI | — | **deny** | all | Customer WiFi internal deny |
| 15 | `deny_cctv_any` | Z-CCTV | — | **deny** | all | CCTV deny all |
| 16 | `deny_dmz_internet` | Z-DMZ | untrust | **deny** | all | DMZ cannot access Internet |
| 17 | `default_deny` | — | — | **deny** | all | Default deny all |

### NAT Configuration

#### Static NAT Server (Port Forwarding)
| Name | Protocol | Global IP | Global Port | Inside IP | Inside Port |
|------|----------|-----------|-------------|-----------|-------------|
| `web_banking_80` | TCP | 200.1.1.10 | 80 | 192.168.50.10 | 80 |
| `web_banking_443` | TCP | 200.1.1.10 | 443 | 192.168.50.10 | 443 |

#### Source NAT (Easy-IP)
| Rule Name | Source Zone | Destination Zone | Source Address | Action |
|-----------|------------|-----------------|----------------|--------|
| `bo_internet` | Z-BACKOFFICE | untrust | 192.168.20.0/24 | easy-ip |
| `custwifi_internet` | Z-CUSTWIFI | untrust | 192.168.40.0/24 | easy-ip |

### Attack Defense
| Feature | Status | Max Rate |
|---------|--------|----------|
| ICMP Flood | ✅ Enabled | 100 pps |
| SYN Flood | ❌ Disabled | 1000 pps |
| UDP Flood | ❌ Disabled | 1000 pps |

---

## 📡 Router Configurations

### R-Internet (Internet Gateway)
```
sysname R-Internet
interface Ethernet0/0/1: IP 200.1.1.100/24 (to Internet)
interface Ethernet0/0/2: IP 100.1.1.1/24 (to internal)
interface LoopBack0: IP 8.8.8.8/32 (simulates Internet)
ip route-static 0.0.0.0/0 → 200.1.1.1 (default route)
ip route-static 100.2.2.0/24 → 200.1.1.200 (to attacker)
```

### R-Attacker (Security Testing PC)
```
sysname R-Attacker
interface GigE0/0/1: IP 200.1.1.200/24 (to Internet side)
interface GigE0/0/2: IP 100.2.2.1/24 (to internal)
ip route-static 0.0.0.0/0 → 200.1.1.1
```

### R-BackOffice (Internal Router)
```
sysname R-BackOffice
interface GigE0/0/1: IP 192.168.20.10/24 (BackOffice LAN)
ip route-static 0.0.0.0/0 → 192.168.20.1
```

---

## 🖼️ Photo Verification Results

### Photo 1: NAT Translation Verification
- `display firewall server-map` confirms static NAT entries
- `display firewall session table` shows HTTP session from 200.1.1.100 → [192.168.50.10:80]
- **Result**: ✅ Internet host reaches 200.1.1.10:80 → translated to 192.168.50.10

### Photo 2: Security Policy Hit Counters
- 17 security policy rules with HIT counters
- Permitted rules have hits: `inbound_webbank` (5), `bo_to_internet` (15), `custwifi_to_internet` (10)
- Denied rules show "background noise": `deny_internet_other` (34), `deny_teller_internet` (10), `default_deny` (421)
- **Result**: ✅ Only intended traffic passes, all others denied and logged

### Photo 3: Teller Internet Access Blocked
- `deny_teller_internet` counter increased from 10 → 15
- `default_deny` counter increased from 421 → 444
- Other counters remained stable (no other violations)
- **Result**: ✅ Teller zone cannot access Internet, every attempt logged

### Photo 4: Firewall Filtering Statistics
- Total received: 17,152 packets
- Discarded by policy: 531 packets
- Route misses: 3 packets
- Unresolved ARP: 12 attempts
- **Result**: ✅ All traffic passes through firewall, no bypass

### Photo 5: FTP Access (BackOffice → FTP Server)
- Source: 192.168.20.10 (Z-BACKOFFICE)
- Destination: 192.168.50.12:21 (SRV-FTP in DMZ)
- Control channel: FTP 230 User logged in
- Data channel: 226 Transfer finished successfully (20,500 bytes)
- **Result**: ✅ `bo_to_ftp` policy works with ASPF

### Photo 6: ASPF in Action (Dynamic Data Channel)
- Control channel: `ftp 192.168.20.10:50581 → 192.168.50.12:21` (permitted by policy)
- Data channel: `tcp-data 192.168.50.12:20 → 192.168.20.10:51129` (opened dynamically by ASPF)
- Without ASPF: data channel would be blocked (from DMZ to internal zone)
- **Result**: ✅ ASPF dynamically opens FTP data channel

---

## ✅ Verification Commands

```bash
# NAT Server Verification
telnet 200.1.1.10 80          (from R-Internet)
telnet 200.1.1.10 23          (should be rejected)

# Database Access Verification  
telnet 192.168.50.11 1521     (from R-Teller)

# FTP Access Verification
ftp 192.168.50.12 → dir       (from R-BackOffice)

# Attack Defense Verification
ping -c 10000 -m 1 200.1.1.1  (from R-Attacker - test ICMP flood defense)

# Firewall Status
display firewall defend flag
display firewall server-map
display firewall session table
display firewall policy all
```

---

## 📊 Network Zones Summary

| Zone Name | Priority | Interface | Subnet | Internet Access |
|-----------|----------|-----------|--------|----------------|
| local | 100 | — | 192.168.0.0/24 | — |
| untrust | 5 | GigE1/0/0 | 200.1.1.0/24 | — |
| Z-TELLER | 84 | GigE1/0/1 | 192.168.10.0/24 | ❌ Denied |
| Z-BACKOFFICE | 80 | GigE1/0/2 | 192.168.20.0/24 | ✅ Permitted |
| Z-ATM | 60 | GigE1/0/3 | 192.168.30.0/24 | ❌ Denied |
| Z-CUSTWIFI | 10 | GigE1/0/4 | 192.168.40.0/24 | ✅ Permitted |
| Z-DMZ | 49 | GigE1/0/5 | 192.168.50.0/24 | ❌ Denied |
| Z-CCTV | 20 | GigE1/0/6 | 192.168.60.0/24 | ❌ Denied |

---

## 🎯 Security Design Principles

1. **Defense in Depth**: Multiple security layers (firewall + zones + policies)
2. **Least Privilege**: Only necessary traffic is permitted between zones
3. **Default Deny**: All unspecified traffic is blocked (`default_deny` rule)
4. **Logging & Monitoring**: All denied traffic is logged for audit
5. **Network Segmentation**: Each department in its own security zone
6. **NAT Protection**: Internal IPs hidden from Internet
7. **ASPF**: Dynamic FTP data channel inspection
8. **Attack Defense**: ICMP flood protection enabled

---

## 📝 File Descriptions — All Files in Project

### `cloud/topology_cloud.json`
ENSP cloud topology definition file containing all nodes (routers, switches, firewalls, PCs, servers) and their connections, positions, initial configurations (IP addresses, routes, zones), security policies, NAT rules, and zone definitions. Used to load the complete network topology in ENSP.

### `configration/FW-Bank.txt`
Complete Huawei USG6525E firewall CLI configuration including:
- Interface IP assignments (8 interfaces)
- Zone definitions (local, untrust, Z-TELLER, Z-BACKOFFICE, Z-ATM, Z-CUSTWIFI, Z-DMZ, Z-CCTV)
- Static routes (default + 2 internal)
- Service object definition (DB_1521 for Oracle on port 1521)
- 17 security policy rules with permit/deny actions
- NAT server rules (web banking port forwarding)
- NAT policy rules (source NAT Easy-IP for BackOffice and Customer WiFi)
- Attack defense settings (ICMP flood enabled)
- AAA management user configuration

### `configration/R-Internet.txt`
Router configuration for the Internet gateway router:
- 2 Ethernet interfaces (200.1.1.100 and 100.1.1.1)
- LoopBack0 with 8.8.8.8 (simulating Internet)
- Default route to 200.1.1.1
- Route to 100.2.2.0/24 via 200.1.1.200

### `configration/R-Attacker.txt`
Router configuration for the attacker/test PC:
- 2 Ethernet interfaces (200.1.1.200 and 100.2.2.1)
- Default route to 200.1.1.1
- Used for penetration testing and attack simulation

### `configration/R-BackOffice.txt`
Router configuration for the BackOffice internal router:
- 1 Ethernet interface (192.168.20.10/24)
- Default route to 192.168.20.1

### `configration/Server.txt`
Table documenting all servers:
- Server-WEB: 192.168.50.10/24, Gateway 192.168.50.1
- Server-DB: 192.168.50.11/24, Gateway 192.168.50.1
- Server-FTP: 192.168.50.12/24, Gateway 192.168.50.1 (credentials: bank/Bank@123)

### `configration/PCs.txt`
Table documenting all PC configurations:
- PC1: 100.1.1.10 (behind R-Internet)
- PC2: 100.2.2.10 (behind R-Attacker)
- PC3: 192.168.40.10 (Customer WiFi / LSW3)
- PC4: 192.168.30.10 (ATM / LSW4)
- PC5: 192.168.60.10 (CCTV / LSW5)
- PC7: 192.168.10.10 (Teller / LSW7)
- PC6: 192.168.20.10 (BackOffice / LSW6)

### `configration/Verification.txt`
List of verification commands to test the network:
- Telnet to verify NAT
- FTP access test from BackOffice
- ICMP flood attack simulation
- Firewall defend status check

### `photo/info.txt`
Detailed descriptions of all 6 verification photos:
- Photo 1: NAT translation proof (server-map + session table)
- Photo 2: Security policy hit counters (17 rules)
- Photo 3: Real-time evidence of Teller internet blocking
- Photo 4: Firewall filtering statistics (17,152 packets)
- Photo 5: FTP session from BackOffice (with ASPF)
- Photo 6: ASPF dynamic data channel demonstration

### `photo/*.png` and `photo/*.jpeg`
Screenshot images from ENSP showing:
- Network topology diagrams
- Interface configurations
- NAT policy configuration
- Security policy configuration
- Zone configuration
- Verification evidence

### `vidoes/New folder.zip`
Compressed video archive containing screen recordings of the project demonstration

### `presention/presentation.pptx`
PowerPoint presentation slides explaining the project

### `presention/presntation.pdf`
PDF version of the presentation

### `ensp projact/*.topo`
15 ENSP project topology save files (binary format) — different versions/saves of the network simulation project

### `ensp projact/topo.topo`
Main ENSP topology file (latest version)

---

## 🔑 Key Technologies Used

| Technology | Purpose |
|-----------|---------|
| **Huawei ENSP** | Network simulation platform |
| **USG6525E Firewall** | Core security appliance |
| **Security Zones** | Network segmentation |
| **NAT Server** | Port forwarding for web banking |
| **Source NAT (Easy-IP)** | Outbound internet access |
| **ASPF** | Application layer FTP inspection |
| **Security Policies** | 17-rule permit/deny matrix |
| **Attack Defense** | ICMP flood protection |
| **AAA** | Admin user management |
| **VRRP** | High availability (configured, standby) |
| **IPsec/SSL VPN** | Configured but not active |

---

## 📈 Project Status

- ✅ Topology configured and verified
- ✅ All firewall rules implemented
- ✅ NAT (static + source) working
- ✅ ASPF for FTP verified
- ✅ Attack defense tested
- ✅ All 6 verification photos captured
- ✅ Presentation prepared (PPTX + PDF)
- ✅ Video documentation available

---

## 📄 License

This project documentation was auto-generated from the ENSP project files.

---

*Generated: September 2026*
