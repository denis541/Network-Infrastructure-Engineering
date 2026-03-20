# Addressing Table — Spanning Tree Protocol Lab

## Router0 (ISR4331)

| Interface      | IP Address    | Subnet Mask   | Description             |
|----------------|---------------|---------------|-------------------------|
| Gig0/0/0.10    | 192.168.10.1  | 255.255.255.0 | VLAN 10 - Sales         |
| Gig0/0/0.20    | 192.168.20.1  | 255.255.255.0 | VLAN 20 - HR            |
| Gig0/0/0.30    | 192.168.30.1  | 255.255.255.0 | VLAN 30 - IT            |
| Gig0/0/0.40    | 192.168.40.1  | 255.255.255.0 | VLAN 40 - Management    |

## Switch Trunk Ports

| Switch  | Trunk Port | Connected To       |
|---------|------------|--------------------|
| Switch1 | Fa0/24     | Router0 Gig0/0/0   |
| Switch1 | Fa0/2      | Switch2 Fa0/1      |
| Switch1 | Fa0/3      | Switch4 Fa0/1      |
| Switch1 | Fa0/1      | Switch3 Fa0/2      |
| Switch2 | Fa0/2      | Switch3 Fa0/1      |
| Switch3 | Fa0/3      | Switch4 Fa0/3      |

## Access Ports

| Switch  | Port  | VLAN | Device |
|---------|-------|------|--------|
| Switch1 | Fa0/4 | 40   | PC0    |
| Switch2 | Fa0/4 | 10   | PC1    |
| Switch3 | Fa0/4 | 30   | PC2    |
| Switch4 | Fa0/4 | 20   | PC3    |

## End Devices

| Device | IP Address   | Subnet Mask   | Default Gateway |
|--------|--------------|---------------|-----------------|
| PC0    | 192.168.40.2 | 255.255.255.0 | 192.168.40.1    |
| PC1    | 192.168.10.2 | 255.255.255.0 | 192.168.10.1    |
| PC2    | 192.168.30.2 | 255.255.255.0 | 192.168.30.1    |
| PC3    | 192.168.20.2 | 255.255.255.0 | 192.168.20.1    |
