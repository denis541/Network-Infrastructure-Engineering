# 08 VLSM Subnetting — KenyaTech Ltd

## Objective
Design and implement a VLSM (Variable Length Subnet Mask) addressing scheme
for KenyaTech Ltd using a single address block, allocating subnets efficiently
based on the number of hosts required per department.

## Topology
- 1 ISR Router (Router-on-a-Stick)
- 4 Cisco 2960-24TT Switches (Switch0, Switch1, Switch2, Switch3)
- 5 PCs (PC0–PC4)

## VLANs
| VLAN ID | Name           | Subnet            | Hosts Supported |
|---------|----------------|-------------------|-----------------|
| 10      | proom          | 172.31.0.0/21     | 2046            |
| 20      | officeStaff    | 172.31.8.0/23     | 510             |
| 30      | securityCamera | 172.31.10.0/24    | 254             |
| 40      | VLAN40         | 172.31.11.0/26    | 62              |

## Key Concepts Demonstrated
- VLSM subnetting to minimize address waste
- Router-on-a-Stick inter-VLAN routing
- PVST+ spanning tree per VLAN
- Subnet sizing based on host requirements
