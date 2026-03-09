# Cisco Packet Tracer Labs

![Cisco](https://img.shields.io/badge/Cisco-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Packet%20Tracer-8.x-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Labs](https://img.shields.io/badge/Labs-2%20Complete-brightgreen?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Blue%20Team%20%2F%20NetSec-0A66C2?style=for-the-badge)

A progressive series of hands-on Cisco networking labs built in Packet Tracer, documented as part of a blue team cybersecurity portfolio. Each lab builds on the previous one using the same base topology, simulating how a real network is built, verified, and secured incrementally.

---

## Lab Series Overview

| Lab | Title | Topics Covered | Status |
|-----|-------|---------------|--------|
| 01 | [STP + Router-on-a-Stick](04_Spanning-Tree-Protocol) | VLANs, Trunking, Inter-VLAN Routing, Spanning Tree, PortFast, BPDU Guard | Complete |
| 02 | [Device Hardening](06_Device-Hardening/Network-Topology.png) | SSH, Port Security, Unused Port Shutdown, Banners, AAA, CDP Disable | Complete |

---

## Base Topology

All labs use the same physical topology. Each lab adds configuration on top of the previous one.

```
                        R1 (Cisco 2911)
                            |
                        SW1 (Core — Cisco 2960)
                       /    |    \
                    SW2     SW3    SW4
                  (Cisco  (Cisco  (Cisco
                   2960)   2960)   2960)
                     |       |       |
                   PC-HR  PC-Fin   PC-IT

PC-Sales connects directly to SW1 Fa0/10

Redundant links between SW2, SW3, SW4 for STP demonstration
```

---

## VLAN Design

| VLAN | Department | Network         | Gateway      |
|------|------------|-----------------|--------------|
| 10   | Sales      | 192.168.10.0/24 | 192.168.10.1 |
| 20   | HR         | 192.168.20.0/24 | 192.168.20.1 |
| 30   | Finance    | 192.168.30.0/24 | 192.168.30.1 |
| 40   | IT         | 192.168.40.0/24 | 192.168.40.1 |

---

## Lab 01 — STP + Router-on-a-Stick

**Objective:** Build a multi-department network with full inter-VLAN communication and loop prevention.

**Key outcomes:**
- Four VLANs segmenting departments at Layer 2
- 802.1Q trunk links carrying all VLANs between switches and to the router
- Router-on-a-stick subinterfaces enabling inter-VLAN routing
- Rapid PVST+ with SW1 as root bridge for all VLANs
- PortFast and BPDU Guard on all access ports
- Real troubleshooting — trunk misconfiguration diagnosed and fixed using `show interfaces switchport`

[View Lab 01 →](./lab-01-stp-router-on-a-stick/README.md)

---

## Lab 02 — Device Hardening

**Objective:** Secure every device in the existing topology against unauthorised access and common Layer 2 attacks.

**Key outcomes:**
- SSH v2 configured on all switches and router, Telnet disabled
- Local user accounts with encrypted passwords
- Login banners warning unauthorised users
- Port security limiting MAC addresses on access ports
- Unused ports shutdown and assigned to a black hole VLAN
- CDP disabled on untrusted interfaces
- Enable secret replacing enable password on all devices
- SSH remote login verified from PC to switch and router CLI

[View Lab 02 →](./lab-02-device-hardening/README.md)

---

## Skills Demonstrated

| Skill | Labs |
|-------|------|
| VLAN configuration and management | 01, 02 |
| 802.1Q trunking | 01, 02 |
| Inter-VLAN routing | 01 |
| Spanning Tree Protocol | 01 |
| Network troubleshooting methodology | 01 |
| Device hardening and access control | 02 |
| SSH configuration and verification | 02 |
| Layer 2 attack mitigation | 02 |
| Running config documentation | 01, 02 |

---

## Tools Used

- Cisco Packet Tracer 8.x
- Cisco IOS CLI
- Cisco NetAcad — Intro to Networks, Network Security, CyberOps

---

## How to Use These Labs

1. Download the `.pkt` file from the lab folder
2. Open it in Cisco Packet Tracer 8.x or later
3. Follow the README for that lab to replicate the configuration
4. Use the `configs/` folder to verify your output against the working configs
5. Use the `screenshots/` folder to see expected verification output

---

## About

Denis O. Onduso[Denis.Onduso@outlook.com] — | Aspiring SOC Analyst / Cloud Security Engineer

Building a hands-on portfolio targeting SOC Tier 1, cloud security, and junior network security roles.

Currently completing: Cisco NetAcad Intro to Networks · Network Security · CyberOps

---

