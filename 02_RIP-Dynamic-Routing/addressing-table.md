# Addressing Table — RIP 3-Router Lab

## Routers

| Device | Interface | IP Address   | Subnet Mask   | Role                  |
|--------|-----------|--------------|---------------|-----------------------|
| R1     | Fa0/0     | 192.168.1.1  | 255.255.255.0 | LAN gateway           |
| R1     | Se0/0/0   | 192.168.2.1  | 255.255.255.0 | WAN link to R2 (DCE)  |
| R2     | Fa0/0     | 192.168.3.1  | 255.255.255.0 | LAN gateway           |
| R2     | Se0/0/0   | 192.168.2.2  | 255.255.255.0 | WAN link to R1 (DTE)  |
| R2     | Se0/0/1   | 192.168.4.2  | 255.255.255.0 | WAN link to R3 (DCE)  |
| R3     | Fa0/0     | 192.168.5.1  | 255.255.255.0 | LAN gateway           |
| R3     | Se0/0/1   | 192.168.4.1  | 255.255.255.0 | WAN link to R2 (DTE)  |

## End Devices

| Device | Interface | IP Address    | Subnet Mask   | Default Gateway |
|--------|-----------|---------------|---------------|-----------------|
| PC1    | NIC       | 192.168.1.10  | 255.255.255.0 | 192.168.1.1     |
| PC2    | NIC       | 192.168.3.10  | 255.255.255.0 | 192.168.3.1     |
| PC3    | NIC       | 192.168.5.10  | 255.255.255.0 | 192.168.5.1     |

## Network Segments

| Segment          | Network Address  | Purpose                  |
|------------------|------------------|--------------------------|
| R1 LAN           | 192.168.1.0/24   | PC1 and R1 Fa0/0         |
| R1 to R2 WAN     | 192.168.2.0/24   | Serial link R1 — R2      |
| R2 LAN           | 192.168.3.0/24   | PC2 and R2 Fa0/0         |
| R2 to R3 WAN     | 192.168.4.0/24   | Serial link R2 — R3      |
| R3 LAN           | 192.168.5.0/24   | PC3 and R3 Fa0/0         |

## DCE/DTE Reference

| Serial Link  | DCE Side | DTE Side | Clock Rate |
|--------------|----------|----------|------------|
| R1 — R2      | R1       | R2       | 64000      |
| R2 — R3      | R2       | R3       | 64000      |
