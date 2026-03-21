# Addressing Table — Lab 06 L3 Switch SVI Inter-VLAN Routing

## Device Inventory

| Device  | Model        | Role                        |
|---------|--------------|-----------------------------|
| SW1     | Cisco 3560   | Multilayer Switch (L2 + L3) |
| PC0     | PC-PT        | End Host — VLAN 10          |
| PC1     | PC-PT        | End Host — VLAN 20          |
| PC2     | PC-PT        | End Host — VLAN 30          |

---

## Port Assignments

| Switch Port | Connected To | Mode   | VLAN |
|-------------|--------------|--------|------|
| Fa0/1       | PC0          | Access | 10   |
| Fa0/2       | PC1          | Access | 20   |
| Fa0/3       | PC2          | Access | 30   |

---

## SVI Interfaces

| Interface | IP Address   | Subnet Mask   | Role               |
|-----------|--------------|---------------|--------------------|
| VLAN 10   | 192.168.10.1 | 255.255.255.0 | Gateway for VLAN 10 |
| VLAN 20   | 192.168.20.1 | 255.255.255.0 | Gateway for VLAN 20 |
| VLAN 30   | 192.168.30.1 | 255.255.255.0 | Gateway for VLAN 30 |

---

## End Host Addressing

| Host | IP Address   | Subnet Mask   | Default Gateway |
|------|--------------|---------------|-----------------|
| PC0  | 192.168.10.2 | 255.255.255.0 | 192.168.10.1    |
| PC1  | 192.168.20.2 | 255.255.255.0 | 192.168.20.1    |
| PC2  | 192.168.30.2 | 255.255.255.0 | 192.168.30.1    |
