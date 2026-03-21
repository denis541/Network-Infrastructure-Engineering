# Addressing Table — Voice VLAN

## Device Inventory

| Device   | Model       | Role                        |
|----------|-------------|-----------------------------|
| SW1      | Cisco 2960  | Access Switch               |
| IP Phone | Cisco IP Phone | VoIP Endpoint — VLAN 30  |
| PC0      | PC-PT       | End Host — VLAN 10          |

---

## Port Assignment

| Switch Port | Connected To | Mode   | Data VLAN | Voice VLAN |
|-------------|--------------|--------|-----------|------------|
| Fa0/1       | IP Phone     | Access | 10        | 30         |

---

## VLAN Database

| VLAN | Name  | Type  | Ports  |
|------|-------|-------|--------|
| 10   | Data  | Data  | Fa0/1  |
| 30   | Voice | Voice | Fa0/1  |

---

## End Host Addressing

| Host     | IP Address   | Subnet Mask   | Default Gateway | VLAN |
|----------|--------------|---------------|-----------------|------|
| PC0      | 192.168.10.2 | 255.255.255.0 | 192.168.10.1    | 10   |
| IP Phone | Auto via CDP | —             | —               | 30   |
