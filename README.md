# Network Infrastructure Engineering    
       
![Cisco Packet Tracer](https://img.shields.io/badge/Tool-Cisco_Packet_Tracer-1BA0D7?style=flat&logoColor=white)          
![Domain](https://img.shields.io/badge/Domain-Network_Engineering-0078D4?style=flat&logoColor=white)               
![Certification](https://img.shields.io/badge/Aligned-Cisco_CCNA_200--301-E01B1B?style=flat&logoColor=white)           
     
**Analyst:** Denis O. Onduso | [GitHub](https://github.com/denis541) | Denis.Onduso@outlook.com  

---

## Overview

Cisco CCNA lab work covering the full routing and switching stack — subnetting, VLAN segmentation, inter-VLAN routing, Spanning Tree Protocol, static routing, switch port security, and ACL configuration. All labs built and documented in Cisco Packet Tracer with topology diagrams and configuration files included for each exercise.

---

## Labs

| Folder | Topic | Key Concepts |
|--------|-------|-------------|
| 01_SMB-Network-Design | Small-to-medium business network | Hierarchical design, VLSM, device hardening, static routing |
| 02_VLAN-and-Trunking | VLAN segmentation | VLAN creation, access ports, 802.1Q trunking, native VLAN |
| 03_Inter-VLAN-Routing | Router-on-a-stick | Subinterfaces, 802.1Q encapsulation, Layer 3 switching |
| 04_Spanning-Tree-Protocol | Loop prevention | Root bridge election, port roles, RSTP convergence |
| 05_Static-Routing | Multi-router connectivity | Static routes, default routes, floating static routes |
| 06_Device-Hardening | Security baseline | Passwords, SSH, banner, port security, encryption |
| 07_ACLs | Traffic filtering | Standard ACLs, extended ACLs, inbound vs outbound placement |
| 08_Topology-Analysis | Logical vs physical design | 3-tier hierarchy, media selection, redundancy validation |

---

## Lab Structure

Each lab contains:

- `.pkt` file — working Packet Tracer topology
- `topology.png` — network layout diagram
- `README.md` — configuration documentation and verification output

---

## CCNA Exam Alignment

| CCNA 200-301 Domain | Coverage |
|--------------------|---------|
| Network Fundamentals | IP addressing, subnetting, Ethernet switching, OSI model |
| Network Access | VLANs, trunking, STP, port security |
| IP Connectivity | Static routing, routing table verification |
| IP Services | DHCP, NAT/PAT basics |
| Security Fundamentals | ACLs, port security, device hardening, SSH |

---

## How to Open Labs

1. Download and install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) — free with a Cisco NetAcad account
2. Clone this repository: `git clone https://github.com/denis541/Network-Infrastructure-Engineering`
3. Open any `.pkt` file in Packet Tracer
4. Refer to the topology diagram and README in the same folder for the expected end state
EOF
