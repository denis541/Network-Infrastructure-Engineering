# VTP (VLAN Trunking Protocol)

![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-blue?style=flat-square&logo=cisco)
![VLANs](https://img.shields.io/badge/VLANs-10%20%7C%2020%20%7C%2030-orange?style=flat-square)
![Protocol](https://img.shields.io/badge/Protocol-VTP%20v2-yellow?style=flat-square)
![Switches](https://img.shields.io/badge/Switches-6-informational?style=flat-square)
![CCNA](https://img.shields.io/badge/CCNA-Aligned-red?style=flat-square)

## Objective

Configure VTP (VLAN Trunking Protocol) across a 6-switch network to demonstrate centralized VLAN management. VLANs are created once on the VTP server and automatically propagated to all client switches through trunk links — eliminating the need for manual VLAN configuration on each switch individually.

---

## Topology

```
                          [SW1 - VTP Server]
                          /               \
                       trunk             trunk
                        /                   \
              [SW2 - VTP Client]     [SW3 - VTP Client]
              /          \                /          \
           trunk        trunk          trunk        trunk
            /              \          /               \
 [SW4 - VTP Client]  [SW5 - VTP Client]       [SW6 - VTP Client]
```

---

## VTP Configuration

| Switch | VTP Mode | VTP Domain | VTP Password |
|--------|----------|------------|--------------|
| SW1    | Server   | KenyaTech  | cisco        |
| SW2    | Client   | KenyaTech  | cisco        |
| SW3    | Client   | KenyaTech  | cisco        |
| SW4    | Client   | KenyaTech  | cisco        |
| SW5    | Client   | KenyaTech  | cisco        |
| SW6    | Client   | KenyaTech  | cisco        |

---

## VLAN Configuration

| VLAN | Name       | Created On |
|------|------------|------------|
| 10   | Sales      | SW1 only   |
| 20   | IT         | SW1 only   |
| 30   | Management | SW1 only   |

---

## Configuration

### SW1 — VTP Server

```bash
# Set VTP mode, domain and password
SW1(config)# vtp mode server
SW1(config)# vtp domain KenyaTech
SW1(config)# vtp password cisco

# Configure trunk links to SW2 and SW3
SW1(config)# interface fa0/1
SW1(config-if)# switchport mode trunk
SW1(config-if)# exit
SW1(config)# interface fa0/2
SW1(config-if)# switchport mode trunk

# Create VLANs — only done here on the server
SW1(config)# vlan 10
SW1(config-vlan)# name Sales
SW1(config)# vlan 20
SW1(config-vlan)# name IT
SW1(config)# vlan 30
SW1(config-vlan)# name Management
```

### SW2 and SW3 — VTP Clients (Distribution Layer)

```bash
# Repeat on both SW2 and SW3
SWx(config)# vtp mode client
SWx(config)# vtp domain KenyaTech
SWx(config)# vtp password cisco

# Trunk ports — one facing up to SW1, two facing down to access switches
SWx(config)# interface fa0/1
SWx(config-if)# switchport mode trunk
SWx(config-if)# exit
SWx(config)# interface fa0/2
SWx(config-if)# switchport mode trunk
SWx(config-if)# exit
SWx(config)# interface fa0/3
SWx(config-if)# switchport mode trunk
```

### SW4, SW5, SW6 — VTP Clients (Access Layer)

```bash
# Repeat on SW4, SW5, SW6
SWx(config)# vtp mode client
SWx(config)# vtp domain KenyaTech
SWx(config)# vtp password cisco

# Single trunk port facing up to distribution switch
SWx(config)# interface fa0/1
SWx(config-if)# switchport mode trunk
```

---

## Verification Commands

```bash
# Check VLANs propagated correctly
SW2# show vlan brief
SW3# show vlan brief
SW4# show vlan brief

# Check VTP status and revision number
SW1# show vtp status
SW2# show vtp status

# Check trunk links are operational
SW1# show interfaces trunk
```

### Expected Output — show vlan brief (on any client switch)

```
VLAN  Name        Status    Ports
----  ----------  --------  -----
1     default     active
10    Sales       active
20    IT          active
30    Management  active
```

### Expected Output — show vtp status

```
VTP Version                     : 2
Configuration Revision          : 6
Maximum VLANs supported locally : 255
Number of existing VLANs        : 8
VTP Operating Mode              : Client
VTP Domain Name                 : KenyaTech
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : ...
```

---

## Key Verification — Revision Number Sync

All six switches must show the **same Configuration Revision number** in `show vtp status`. A matching revision number confirms all switches have the same VLAN database and are fully in sync with the server.

| Switch | Expected Revision |
|--------|-------------------|
| SW1    | 6                 |
| SW2    | 6                 |
| SW3    | 6                 |
| SW4    | 6                 |
| SW5    | 6                 |
| SW6    | 6                 |

---

## Client Mode Restriction Test

Attempting to create a VLAN directly on any client switch produces:

```
SW2(config)# vlan 99
VTP VLAN configuration not allowed when device is in client mode.
```

This confirms client switches are locked from local VLAN management — all changes must go through the VTP server.

---

## Security Risk — VTP Revision Number Attack

VTP is convenient but carries a well-known risk worth understanding from a blue team perspective.

**The scenario:** A switch that was previously used elsewhere may have a high revision number saved in its config. If that switch is plugged into your network as a client with a matching domain name and password, all other switches will accept its VLAN database because they see a higher revision number — even though it came from a client. This can wipe your entire production VLAN database.

**Mitigations:**
- Always reset the revision number on a switch before adding it to the network by temporarily changing the VTP domain name then changing it back — this resets the counter to 0
- Use VTP version 3 which has better protection against this
- Use VTP Transparent mode on switches that don't need to participate in VTP

---

## Concepts Covered

- VTP modes — Server, Client, Transparent
- VTP domain and password — must match exactly across all switches for propagation to work
- VTP revision number — tracks VLAN database version, higher number wins
- Trunk links as the transport path for VTP advertisements
- Client mode enforcement — clients cannot create, edit, or delete VLANs locally
- VTP revision number attack — a critical real-world security consideration
