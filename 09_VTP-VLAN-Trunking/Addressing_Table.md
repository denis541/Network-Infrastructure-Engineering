# Addressing Table — Lab 04 VTP VLAN Trunking

## VTP Configuration

| Switch | VTP Mode | VTP Domain | VTP Password | Layer |
|--------|----------|------------|--------------|-------|
| SW1    | Server   | KenyaTech  | cisco        | Core  |
| SW2    | Client   | KenyaTech  | cisco        | Distribution |
| SW3    | Client   | KenyaTech  | cisco        | Distribution |
| SW4    | Client   | KenyaTech  | cisco        | Access |
| SW5    | Client   | KenyaTech  | cisco        | Access |
| SW6    | Client   | KenyaTech  | cisco        | Access |

---

## Trunk Links

| From | Port  | To  | Port  | Mode  |
|------|-------|-----|-------|-------|
| SW1  | Fa0/1 | SW2 | Fa0/1 | Trunk |
| SW1  | Fa0/2 | SW3 | Fa0/1 | Trunk |
| SW2  | Fa0/2 | SW4 | Fa0/1 | Trunk |
| SW2  | Fa0/3 | SW5 | Fa0/1 | Trunk |
| SW3  | Fa0/2 | SW6 | Fa0/1 | Trunk |

---

## VLAN Database

| VLAN | Name       | Created On | Propagated To        |
|------|------------|------------|----------------------|
| 10   | Sales      | SW1        | SW2, SW3, SW4, SW5, SW6 |
| 20   | IT         | SW1        | SW2, SW3, SW4, SW5, SW6 |
| 30   | Management | SW1        | SW2, SW3, SW4, SW5, SW6 |

---

## VTP Revision Number

| Switch | Revision Number | Status  |
|--------|-----------------|---------|
| SW1    | 6               | In Sync |
| SW2    | 6               | In Sync |
| SW3    | 6               | In Sync |
| SW4    | 6               | In Sync |
| SW5    | 6               | In Sync |
| SW6    | 6               | In Sync |

> All switches must show the same revision number to confirm the VLAN database is fully synchronized across the network.
