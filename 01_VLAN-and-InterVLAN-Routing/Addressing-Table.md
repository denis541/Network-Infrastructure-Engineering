# Addressing Table — VLAN and InterVLAN Routing

## Router0 (ISR4331)

| Interface        | IP Address      | Subnet Mask     | Description          |
|------------------|-----------------|-----------------|----------------------|
| Gig0/0/0.10      | 192.168.10.1    | 255.255.255.0   | VLAN 10 subinterface |
| Gig0/0/0.20      | 192.168.20.1    | 255.255.255.0   | VLAN 20 subinterface |

## Switch0 (2960-24TT)

| Interface | Mode   | VLAN        |
|-----------|--------|-------------|
| Fa0/5     | Trunk  | All VLANs   |
| Fa0/1     | Access | VLAN 10     |
| Fa0/2     | Access | VLAN 10     |
| Fa0/3     | Access | VLAN 20     |
| Fa0/4     | Access | VLAN 20     |

## End Devices

| Device | Interface | IP Address    | Subnet Mask     | Default Gateway |
|--------|-----------|---------------|-----------------|-----------------|
| PC0    | Fa0       | 192.168.10.2  | 255.255.255.0   | 192.168.10.1    |
| PC1    | Fa0       | 192.168.10.3  | 255.255.255.0   | 192.168.10.1    |
| PC2    | Fa0       | 192.168.20.2  | 255.255.255.0   | 192.168.20.1    |
| PC3    | Fa0       | 192.168.20.3  | 255.255.255.0   | 192.168.20.1    |
