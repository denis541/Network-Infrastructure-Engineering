# Addressing Table — Lab 05 Native VLAN Mismatch

## Switch Inventory

| Switch | Model | Role |
|--------|-------|------|
| SW1    | 2960  | Switch 1 |
| SW2    | 2960  | Switch 2 |

---

## Trunk Link

| From | Port  | To  | Port  | Mode  |
|------|-------|-----|-------|-------|
| SW1  | Fa0/1 | SW2 | Fa0/1 | Trunk |

---

## Native VLAN States

| Stage         | SW1 Native VLAN | SW2 Native VLAN | Status    |
|---------------|-----------------|-----------------|-----------|
| Initial       | 1 (default)     | 1 (default)     | ✅ Match  |
| After break   | 99              | 1               | ❌ Mismatch |
| After fix     | 99              | 99              | ✅ Match  |
