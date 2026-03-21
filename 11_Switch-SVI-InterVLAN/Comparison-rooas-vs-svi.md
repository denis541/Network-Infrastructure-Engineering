# Comparison — Router-on-a-Stick vs L3 Switch SVI

Two approaches to inter-VLAN routing, both achieving the same result through different means. This document compares them based on the hands-on labs completed in this repository.

---

## Side by Side

| | Router-on-a-Stick (Lab 03) | L3 Switch SVI (Lab 06) |
|---|---|---|
| Hardware required | Router + Switch | Multilayer switch only |
| Where routing happens | On the router (external) | Inside the switch (internal) |
| Physical trunk link needed | Yes — between router and switch | No |
| Configuration complexity | Subinterfaces + encapsulation dot1q | SVIs + ip routing |
| Traffic path | PC → Switch → Router → Switch → PC | PC → Switch → PC |
| Performance | Lower — traffic leaves switch twice | Higher — routing in hardware |
| Scalability | Limited by router interfaces | Scales easily with more SVIs |
| Cost | Higher — needs separate router | Lower — one device |
| Best used for | Small networks, when a router is already present | Enterprise access/distribution layer |

---

## Traffic Flow Comparison

### Router-on-a-Stick
```
PC0 (VLAN 10) → Switch (Layer 2 forward) → Router subinterface Gi0/0/0.10
→ Router routes to Gi0/0/0.20 → Switch (Layer 2 forward) → PC1 (VLAN 20)
```
Traffic physically leaves the switch, gets routed by the router, then re-enters the switch.

### L3 Switch SVI
```
PC0 (VLAN 10) → 3560 (Layer 2 to SVI Vlan10) → 3560 routes internally to SVI Vlan20
→ 3560 (Layer 2 forward) → PC1 (VLAN 20)
```
Traffic never leaves the switch. Routing happens internally between SVIs.

---

## Configuration Comparison

### Router-on-a-Stick — Key Commands
```bash
# Router subinterfaces
Router(config)# interface g0/0/0.10
Router(config-subif)# encapsulation dot1q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

# Switch trunk to router
SW(config)# interface fa0/5
SW(config-if)# switchport mode trunk
```

### L3 Switch SVI — Key Commands
```bash
# SVIs on multilayer switch
SW(config)# interface vlan 10
SW(config-if)# ip address 192.168.10.1 255.255.255.0
SW(config-if)# no shutdown

# Enable routing
SW(config)# ip routing
```

---

## Common Mistake — SVIs on a 2960

In Lab 03, SVIs were accidentally configured with IP addresses on the 2960 switch alongside the router subinterfaces. This caused a conflict — both devices competed to be the gateway for the same VLANs, breaking inter-VLAN routing entirely.

**Rule:** SVIs with IP addresses belong on a multilayer switch (3560, 3750, etc.) only. On a standard 2960 in a Router-on-a-Stick topology, never assign IPs to VLAN interfaces.

---

## When to Use Which

**Use Router-on-a-Stick when:**
- You already have a router in the topology
- The network is small (2-3 VLANs)
- Budget doesn't allow a multilayer switch
- You need the router for other functions like NAT or WAN connectivity

**Use L3 Switch SVIs when:**
- You are designing a new network from scratch
- Performance matters — high traffic between VLANs
- You have more than 3-4 VLANs
- This is an enterprise access or distribution layer deployment
