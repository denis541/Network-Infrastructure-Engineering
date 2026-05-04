# Lab 03 — Router-on-a-Stick Inter-VLAN Routing

![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-blue?style=flat-square&logo=cisco)
![VLANs](https://img.shields.io/badge/VLANs-10%20%7C%2020-orange?style=flat-square)
![Routing](https://img.shields.io/badge/Routing-Inter--VLAN-informational?style=flat-square) 
![Protocol](https://img.shields.io/badge/Protocol-802.1Q-yellow?style=flat-square)
![CCNA](https://img.shields.io/badge/CCNA-Aligned-red?style=flat-square)

## Objective

Configure inter-VLAN routing using a single router interface divided into subinterfaces (Router-on-a-Stick), allowing hosts in different VLANs to communicate through a Layer 3 device.

---

## Topology

```
                        [Router0 - ISR4331]
                               |
                          Gig0/0/0 (trunk)
                               |
                          Fa0/5 (trunk)
                        [Switch0 - 2960]
                    /        |        \        \
                Fa0/1     Fa0/2     Fa0/3     Fa0/4
                  |          |         |          |
                PC0         PC1       PC2        PC3
             (VLAN 10)  (VLAN 10)  (VLAN 20)  (VLAN 20)
```

---

## VLAN Configuration

| VLAN | Name  | Subnet           | Gateway       |
|------|-------|------------------|---------------|
| 10   | Sales | 192.168.10.0/24  | 192.168.10.1  |
| 20   | IT    | 192.168.20.0/24  | 192.168.20.1  |

---

## IP Addressing Table

| Device    | Interface | IP Address     | Subnet Mask     | Default Gateway |
|-----------|-----------|----------------|-----------------|-----------------|
| Router0   | Gig0/0/0.10 | 192.168.10.1 | 255.255.255.0   | —               |
| Router0   | Gig0/0/0.20 | 192.168.20.1 | 255.255.255.0   | —               |
| PC0       | Fa0        | 192.168.10.2   | 255.255.255.0   | 192.168.10.1    |
| PC1       | Fa0        | 192.168.10.3   | 255.255.255.0   | 192.168.10.1    |
| PC2       | Fa0        | 192.168.20.2   | 255.255.255.0   | 192.168.20.1    |
| PC3       | Fa0        | 192.168.20.3   | 255.255.255.0   | 192.168.20.1    |

---

## Configuration

### Switch0

```bash
# Create VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name Sales
Switch(config)# vlan 20
Switch(config-vlan)# name IT

# Assign access ports
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

Switch(config)# interface fa0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10

Switch(config)# interface fa0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20

Switch(config)# interface fa0/4
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20

# Configure trunk port to router
Switch(config)# interface fa0/5
Switch(config-if)# switchport mode trunk
```

### Router0

```bash
# Bring up physical interface
Router(config)# interface gig0/0/0
Router(config-if)# no shutdown

# Subinterface for VLAN 10
Router(config)# interface gig0/0/0.10
Router(config-subif)# encapsulation dot1q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# no shutdown

# Subinterface for VLAN 20
Router(config)# interface gig0/0/0.20
Router(config-subif)# encapsulation dot1q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# no shutdown
```

---

## Verification Commands

```bash
# Router — confirm subinterfaces are up with correct IPs
Router# show ip interface brief

# Router — confirm routing table has both networks
Router# show ip route

# Switch — confirm VLANs and port assignments
Switch# show vlan brief

# Switch — confirm trunk is operational
Switch# show interfaces fa0/5 switchport
```

### Expected Output — show ip interface brief

```
GigabitEthernet0/0/0       unassigned    YES  unset  up  up
GigabitEthernet0/0/0.10    192.168.10.1  YES  manual up  up
GigabitEthernet0/0/0.20    192.168.20.1  YES  manual up  up
```

### Expected Output — show ip route

```
C    192.168.10.0/24 is directly connected, GigabitEthernet0/0/0.10
C    192.168.20.0/24 is directly connected, GigabitEthernet0/0/0.20
```

---

## Connectivity Test Results

| Source | Destination       | Expected | Result |
|--------|-------------------|----------|--------|
| PC0    | PC1 (VLAN 10)     | ✅ Pass  | Pass   |
| PC2    | PC3 (VLAN 20)     | ✅ Pass  | Pass   |
| PC0    | PC2 (VLAN 10→20)  | ✅ Pass  | Pass   |
| PC1    | PC3 (VLAN 10→20)  | ✅ Pass  | Pass   |

---

## Troubleshooting — Issue Encountered

### Symptom
Router could receive pings from PCs but could not ping PCs back. Cross-VLAN communication was failing despite correct subinterface IPs and routing table.

### Root Cause
`interface vlan 10` and `interface vlan 20` SVIs had been configured with IP addresses on the switch. This created a conflict — both the switch and the router were attempting to act as the Layer 3 gateway for the same VLANs. The switch SVIs were intercepting return traffic before it could be properly routed.

### Fix
Removed the SVIs from the switch:
```bash
Switch(config)# no interface vlan 10
Switch(config)# no interface vlan 20
```
This left the router as the sole Layer 3 device. Cross-VLAN pings succeeded immediately after.

### Key Lesson
In a Router-on-a-Stick topology, the switch operates purely at Layer 2 — no IP addresses on VLAN interfaces. SVIs with IPs belong on a Layer 3 switch topology (inter-VLAN routing via SVI method), not on a standard 2960 acting as an access/trunk switch.

---

## Concepts Covered

- 802.1Q VLAN tagging and trunk links
- Router subinterface configuration
- `encapsulation dot1q` — must be configured before the IP address on each subinterface
- Default gateway requirement for inter-VLAN routing (PCs must point to the router subinterface IP)
- Difference between Router-on-a-Stick (subinterfaces on router) and SVI method (Layer 3 switch)
- VLAN isolation at Layer 2 — a switch alone cannot route between VLANs
