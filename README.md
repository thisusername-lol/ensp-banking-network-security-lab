# 🏦 Huawei eNSP Banking Network Security Lab

[![Huawei eNSP](https://img.shields.io/badge/Simulator-Huawei%20eNSP-red)](https://support.huawei.com/enterprise/en/)
[![Firewall](https://img.shields.io/badge/Firewall-USG6525E-blue)](https://support.huawei.com/enterprise/en/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A documented Huawei eNSP banking-network simulation focused on segmentation, least privilege, DMZ protection, NAT, FTP ASPF inspection, and attack-defense verification.

> **Lab only:** The addresses, credentials, and attack simulations in this repository are intended for the isolated eNSP environment. Do not reuse them in a production network.

## Contents

- [Overview](#overview)
- [Topology](#topology)
- [Addressing plan](#addressing-plan)
- [Security model](#security-model)
- [NAT and services](#nat-and-services)
- [Running the lab](#running-the-lab)
- [Verification](#verification)
- [Repository map](#repository-map)
- [Known notes](#known-notes)

## Overview

The lab places a Huawei USG6525E firewall between the simulated Internet and multiple banking network segments. Each department is assigned a separate security zone and traffic is allowed only when it matches an explicit business requirement.

### Demonstrated capabilities

- Multi-zone firewall segmentation
- Default-deny security policy design
- Public web publishing with NAT Server
- Source NAT / Easy-IP for selected internal zones
- DMZ isolation for web, database, and FTP services
- FTP ASPF inspection and dynamic data-channel handling
- ICMP flood defense and blacklist support
- Policy counters, logging, session, and server-map verification

## Topology

```text
                 Simulated Internet
             100.1.1.0/24 / 8.8.8.8
                        |
             R-Internet 200.1.1.100
                        |
                 Bank-FW 200.1.1.1
        _____________|____________________________
       |        |       |       |       |         |
    Teller   BackOffice  ATM  CustWiFi  DMZ      CCTV
  .10.0/24  .20.0/24  .30.0/24 .40.0/24 .50.0/24 .60.0/24
                                      |
                         WEB / DB / FTP servers

 R-Attacker: 200.1.1.200, test LAN 100.2.2.0/24
```

The authoritative topology image is available in [`photo/topolgy ensp.png`](photo/topolgy%20ensp.png), and the working eNSP project is [`ensp projact/topo.topo`](ensp%20projact/topo.topo).

## Addressing plan

| Segment / device | Address | Purpose |
|---|---:|---|
| Firewall untrust | `200.1.1.1/24` | External transit |
| R-Internet | `200.1.1.100/24` | Internet-side router |
| R-Attacker | `200.1.1.200/24` | Security-test router |
| Public web address | `200.1.1.10` | NAT Server for HTTP/HTTPS |
| Teller | `192.168.10.0/24` | Teller workstations |
| BackOffice | `192.168.20.0/24` | Back-office systems |
| ATM | `192.168.30.0/24` | ATM segment |
| Customer WiFi | `192.168.40.0/24` | Guest/customer segment |
| DMZ | `192.168.50.0/24` | Public and application servers |
| CCTV | `192.168.60.0/24` | Camera segment |
| Web server | `192.168.50.10` | HTTP/HTTPS |
| Database server | `192.168.50.11` | TCP/1521 |
| FTP server | `192.168.50.12` | FTP/TCP 21 |

Full endpoint details are in [`configration/PCs.txt`](configration/PCs.txt) and [`configration/Server.txt`](configration/Server.txt).

## Security model

The firewall configuration contains **16 active security-policy rules**. The policy order is important: specific permits appear before broader deny rules, followed by a global default deny.

| Source | Allowed destination/service | Result |
|---|---|---|
| Internet | DMZ web server, HTTP/HTTPS | Permit |
| Teller | DMZ database, TCP/1521 | Permit |
| BackOffice | DMZ web, DB, FTP; CCTV HTTP/RTSP; Internet | Permit |
| ATM | DMZ database, TCP/1521 | Permit |
| Customer WiFi | Internet | Permit |
| CCTV | Any routed destination | Deny |
| DMZ | Internet | Deny |
| Any unmatched flow | Any destination | Deny and log |

The complete CLI is in [`configration/FW-Bank.txt`](configration/FW-Bank.txt).

## NAT and services

### Static NAT Server

| Public endpoint | Private endpoint |
|---|---|
| `200.1.1.10:80` | `192.168.50.10:80` |
| `200.1.1.10:443` | `192.168.50.10:443` |

### Source NAT

Easy-IP is configured for:

- `192.168.20.0/24` (BackOffice) to `untrust`
- `192.168.40.0/24` (Customer WiFi) to `untrust`

### Attack defense

- ICMP flood defense: enabled, threshold `100`
- ICMP redirect defense: enabled
- Blacklist: enabled

## Running the lab

1. Install a compatible Huawei eNSP release and required device templates.
2. Open the `.topo` file from [`ensp projact/`](ensp%20projact/).
3. Start the devices and wait for interfaces to become operational.
4. Apply or compare the device configurations in [`configration/`](configration/).
5. Configure server services and endpoint IP settings from the inventory files.
6. Run the checks in [`configration/Verification.txt`](configration/Verification.txt).
7. Compare the results with the evidence in [`photo/`](photo/).

Compatibility can vary by eNSP and USG image version; always test commands in the simulator before applying them to another environment.

## Verification

Useful Huawei firewall commands:

```text
display firewall defend flag
display firewall server-map
display firewall session table
display firewall policy all
```

Functional tests include:

```text
telnet 200.1.1.10 80
telnet 200.1.1.10 23
 telnet 192.168.50.11 1521
ftp 192.168.50.12
test ping -c 10000 -m 1 200.1.1.1
```

Use the ICMP flood test only inside the eNSP lab. The expected results and evidence descriptions are documented in [`photo/info.txt`](photo/info.txt).

## Repository map

| Path | Description |
|---|---|
| [`configration/`](configration/) | Firewall, router, endpoint, server, and verification files |
| [`ensp projact/`](ensp%20projact/) | Raw eNSP project and device state files |
| [`photo/`](photo/) | Topology, policy, NAT, zone, and verification evidence |
| [`presention/`](presention/) | PPTX and PDF presentation |
| [`LICENSE`](LICENSE) | MIT license |

## Known notes

- The original project folder names (`configration`, `presention`, and `ensp projact`) are preserved to avoid breaking existing eNSP/project links.
- `R-Attacker .txt` is the original uploaded filename; [`R-Attacker.txt`](configration/R-Attacker.txt) is also provided as a clean, space-free copy.
- The FTP username and password in the inventory are demonstration credentials only and must not be reused outside the lab.
- Binary eNSP files should be opened through eNSP rather than edited as text.

## License

Distributed under the [MIT License](LICENSE).
