# Layer 3 Switch Inter-VLAN Routing (SVI Method)

![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-blue?style=flat-square&logo=cisco)
![VLANs](https://img.shields.io/badge/VLANs-10%20%7C%2020%20%7C%2030-orange?style=flat-square)
![Routing](https://img.shields.io/badge/Routing-Layer%203%20Switch-informational?style=flat-square)
![Protocol](https://img.shields.io/badge/Protocol-SVI-yellow?style=flat-square)
![CCNA](https://img.shields.io/badge/CCNA-Aligned-red?style=flat-square)

## Objective

Configure inter-VLAN routing using a Cisco 3560 multilayer switch with Switched Virtual Interfaces (SVIs). Unlike Router-on-a-Stick (Lab 03), no external router is needed — the switch handles both Layer 2 switching and Layer 3 routing internally.

---

## Topology

```
        [Cisco 3560 Multilayer Switch]
        /            |            \
     Fa0/1         Fa0/2         Fa0/3
       |              |              |
      PC0            PC1            PC2
   (VLAN 10)      (VLAN 20)      (VLAN 30)
```

---

## VLAN Configuration

| VLAN | Name       | Subnet          | SVI Gateway   |
|------|------------|-----------------|---------------|
| 10   | Sales      | 192.168.10.0/24 | 192.168.10.1  |
| 20   | IT         | 192.168.20.0/24 | 192.168.20.1  |
| 30   | Management | 192.168.30.0/24 | 192.168.30.1  |

---

## IP Addressing Table

| Device | Interface    | IP Address    | Subnet Mask   | Default Gateway |
|--------|--------------|---------------|---------------|-----------------|
| 3560   | VLAN 10 SVI  | 192.168.10.1  | 255.255.255.0 | —               |
| 3560   | VLAN 20 SVI  | 192.168.20.1  | 255.255.255.0 | —               |
| 3560   | VLAN 30 SVI  | 192.168.30.1  | 255.255.255.0 | —               |
| PC0    | Fa0          | 192.168.10.2  | 255.255.255.0 | 192.168.10.1    |
| PC1    | Fa0          | 192.168.20.2  | 255.255.255.0 | 192.168.20.1    |
| PC2    | Fa0          | 192.168.30.2  | 255.255.255.0 | 192.168.30.1    |

---

## Configuration

### Step 1 — Create VLANs

```bash
SW(config)# vlan 10
SW(config-vlan)# name Sales
SW(config)# vlan 20
SW(config-vlan)# name IT
SW(config)# vlan 30
SW(config-vlan)# name Management
```

### Step 2 — Assign Access Ports

```bash
SW(config)# interface fa0/1
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 10

SW(config)# interface fa0/2
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 20

SW(config)# interface fa0/3
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 30
```

### Step 3 — Configure SVIs

```bash
SW(config)# interface vlan 10
SW(config-if)# ip address 192.168.10.1 255.255.255.0
SW(config-if)# no shutdown

SW(config)# interface vlan 20
SW(config-if)# ip address 192.168.20.1 255.255.255.0
SW(config-if)# no shutdown

SW(config)# interface vlan 30
SW(config-if)# ip address 192.168.30.1 255.255.255.0
SW(config-if)# no shutdown
```

### Step 4 — Enable IP Routing

```bash
SW(config)# ip routing
```

This command is disabled by default on multilayer switches. Without it SVIs have IP addresses but the switch will not route between VLANs.

---

## Verification Commands

```bash
# Confirm SVIs are up with correct IPs
SW# show ip interface brief

# Confirm routing table has all three networks
SW# show ip route

# Confirm VLANs and port assignments
SW# show vlan brief
```

### Expected Output — show ip interface brief

```
Vlan10    192.168.10.1    YES manual up up
Vlan20    192.168.20.1    YES manual up up
Vlan30    192.168.30.1    YES manual up up
```

### Expected Output — show ip route

```
C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
C    192.168.30.0/24 is directly connected, Vlan30
```

---

## Connectivity Test Results

| Source | Destination      | Expected | Result |
|--------|------------------|----------|--------|
| PC0    | PC1 (VLAN 10→20) | ✅ Pass  | Pass   |
| PC0    | PC2 (VLAN 10→30) | ✅ Pass  | Pass   |
| PC1    | PC2 (VLAN 20→30) | ✅ Pass  | Pass   |

---

## Concepts Covered

- SVI (Switched Virtual Interface) — a virtual Layer 3 interface representing a VLAN on a multilayer switch
- `ip routing` — must be explicitly enabled on a multilayer switch to allow inter-VLAN routing
- Layer 3 switching — routing decisions made in hardware inside the switch, faster than router-based routing
- Multilayer switch vs standard switch — a 3560 operates at both Layer 2 and Layer 3, a 2960 operates at Layer 2 only
- SVI method vs Router-on-a-Stick — see `comparison-rooas-vs-svi.md` for a full breakdown
