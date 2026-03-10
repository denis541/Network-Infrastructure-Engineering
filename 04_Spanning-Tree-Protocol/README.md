 # Cisco Packet Tracer Lab — STP + Router-on-a-Stick eeedddddffr dhhhhdhjjjjdjd
![Cisco](https://img.shields.io/badge/Cisco-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Packet%20Tracer-8.x-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![STP](https://img.shields.io/badge/STP-Rapid%20PVST%2B-orange?style=for-the-badge)
![VLANs](https://img.shields.io/badge/VLANs-10%20%7C%2020%20%7C%2030%20%7C%2040-006400?style=for-the-badge)
![Routing](https://img.shields.io/badge/Routing-Router--on--a--Stick-0A66C2?style=for-the-badge)


**Technologies:** VLANs · Trunking · Router-on-a-Stick · PVST+ · Rapid PVST+ · PortFast · BPDU Guard  
**Tool:** Cisco Packet Tracer 8.x  
**Level:** CCNA / BSIT Networking  

---

## Overview

This lab builds a multi-department enterprise network using four Cisco 2960 switches and one 2911 router. It demonstrates VLAN segmentation, inter-VLAN routing via router-on-a-stick, and Spanning Tree Protocol (STP) with redundant switch links to prevent network loops.

The lab was built and troubleshot from scratch, including diagnosing and fixing a trunk misconfiguration that prevented inter-VLAN communication.

---

## Topology

```
                        R1 (Gig0/0)
                            |
                    sales -SW1 (Core)
                       /  |  |  \
                    SW2  SW3  SW4
                     |    |    |
                   HR IT managemt

PC-Sales connects directly to SW1

Redundant links (for STP):
SW2 Fa0/2 <--> SW3 Fa0/2
SW3 Fa0/3 <--> SW4 Fa0/2
SW2 Fa0/3 <--> SW4 Fa0/3
```

---

## VLAN Design

| VLAN | Department | Network          | Gateway       |
|------|------------|------------------|---------------|
| 10   | Sales      | 192.168.10.0/24  | 192.168.10.1  |
| 20   | HR         | 192.168.20.0/24  | 192.168.20.1  |
| 30   | Finance    | 192.168.30.0/24  | 192.168.30.1  |
| 40   | IT         | 192.168.40.0/24  | 192.168.40.1  |

---

## Device Inventory

| Device | Model        | Role                        |
|--------|--------------|-----------------------------|
| R1     | Cisco 2911   | Inter-VLAN router           |
| SW1    | Cisco 2960   | Core switch / STP root bridge |
| SW2    | Cisco 2960   | Access switch — HR          |
| SW3    | Cisco 2960   | Access switch — Finance     |
| SW4    | Cisco 2960   | Access switch — IT          |
| PC-Sales | Generic PC | VLAN 10 end device          |
| PC-HR    | Generic PC | VLAN 20 end device          |
| PC-Finance | Generic PC | VLAN 30 end device        |
| PC-IT    | Generic PC | VLAN 40 end device          |

---

## What Was Configured

### 1. VLANs
Created and named VLANs 10, 20, 30, 40 on all four switches.

### 2. Access Ports
Assigned each PC-facing port to its correct VLAN using `switchport mode access` and `switchport access vlan`.

### 3. Trunk Links
Configured all switch-to-switch and switch-to-router links as 802.1Q trunks using `switchport mode trunk` to carry all VLANs over a single cable.

### 4. Router-on-a-Stick
Configured four subinterfaces on R1 Gig0/0 — one per VLAN — each with `encapsulation dot1q` and an IP address acting as the default gateway for that department.

### 5. Spanning Tree Protocol
- Enabled **Rapid PVST+** on all switches
- Set SW1 as **root bridge** for all VLANs using `spanning-tree vlan X root primary`
- Applied **PortFast** and **BPDU Guard** on all PC-facing access ports
- Added redundant links between SW2, SW3, SW4 to create loops for STP to manage
- Verified blocked ports using `show spanning-tree vlan 10`

---

## Troubleshooting Encountered

**Problem:** PCs could not ping their default gateways despite correct IP configuration.

**Diagnosis:** Ran `show interfaces fa0/24 switchport` on SW1. Output showed:
```
Operational Mode: static access
```
The uplink port from SW1 to R1 was configured as an access port instead of a trunk. This meant only VLAN 1 traffic was reaching the router — all other VLAN traffic was being dropped at SW1.

**Fix:**
```
SW1(config)# interface fa0/24
SW1(config-if)# switchport mode trunk
```

**Result:** All PCs immediately able to ping their gateways and each other across VLANs.

**Lesson:** Always verify trunk operational mode with `show interfaces trunk` after configuring inter-switch and switch-to-router links. A port can be *configured* as trunk but still show as access if there is a mismatch or the command was not applied correctly.

---

## Verification Commands Used

```bash
# Confirm VLANs exist and ports are assigned correctly
show vlan brief

# Confirm trunk links are operational
show interfaces trunk

# Confirm STP root bridge and port states per VLAN
show spanning-tree vlan 10
show spanning-tree vlan 20

# Confirm blocked ports exist (redundant topology)
show spanning-tree blockedports

# Confirm router subinterfaces are up
show ip interface brief

# End-to-end connectivity test
ping 192.168.20.2   (from PC-Sales to PC-HR)
ping 192.168.40.2   (from PC-Sales to PC-IT)
```

---

## Key Concepts Demonstrated

**Why trunking matters:** Without trunk links, each VLAN would need its own dedicated physical cable between every switch and to the router. Trunking allows all VLANs to share one cable using 802.1Q tags to keep traffic separated.

**Why router-on-a-stick:** Switches operate at Layer 2 and cannot route between VLANs on their own. The router acts as the gateway between VLANs — traffic from Sales destined for HR goes up to R1, gets routed, and comes back down to HR.

**Why STP:** Redundant switch links prevent single points of failure but create loops. Without STP, a single broadcast frame would circulate forever and collapse the network in seconds. STP logically blocks redundant ports while keeping them ready as backup paths.

**Why PortFast + BPDU Guard:** End devices like PCs do not participate in STP. Making them wait 30 seconds for STP to converge every time they connect is unnecessary. PortFast skips the wait. BPDU Guard protects against someone accidentally plugging a switch into an access port and disrupting the STP topology.

---

## Files in This Repository

```
├── README.md               — This file
├── lab-file.pkt            — Packet Tracer topology file
├── configs/
│   ├── R1-config.txt       — Router running configuration
│   ├── SW1-config.txt      — Core switch running configuration
│   ├── SW2-config.txt      — SW2 running configuration
│   ├── SW3-config.txt      — SW3 running configuration
│   └── SW4-config.txt      — SW4 running configuration
└── screenshots/
    ├── topology.png         — Full Packet Tracer topology
    ├── spanning-tree.png    — show spanning-tree output with BLK port
    └── ping-verification.png — Successful cross-VLAN pings
```

---

## Author

Denis — [Denis.Onduso@outlook.com]| Aspiring SOC Analyst / Cloud Security Engineer  
Cisco NetAcad: CCNA Networks · Network Security · CyberOps
