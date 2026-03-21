# Troubleshooting — Native VLAN Mismatch

## Issue

Native VLAN mismatch on trunk link between SW1 and SW2.

---

## Symptom

Immediately after changing the native VLAN on SW1 to 99, the following CDP warning appeared on the console:

```
%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on 
FastEthernet0/1 (99), with Switch FastEthernet0/1 (1).
```

---

## Root Cause

The native VLAN was changed on SW1 only. SW1 was now tagging untagged traffic as VLAN 99 while SW2 was still expecting untagged traffic to belong to VLAN 1. Both sides of a trunk must agree on the same native VLAN — when they don't, untagged frames arriving on one switch get placed into the wrong VLAN on the other side.

---

## Impact

- Untagged traffic from SW1 VLAN 99 arrives on SW2 as VLAN 1 traffic
- Hosts in VLAN 99 on SW1 side effectively communicate with VLAN 1 hosts on SW2 side without intending to
- No connectivity error is thrown to end users — the mismatch is silent from a host perspective
- CDP detects and warns but does not block traffic

---

## Security Implication — VLAN Hopping

A native VLAN mismatch is the basis of a **VLAN hopping attack**. An attacker on VLAN 99 can send untagged frames that cross the trunk and land in VLAN 1 on the other switch, bypassing VLAN segmentation entirely. This is why native VLAN mismatches are treated as a security misconfiguration, not just an operational one.

**Best practice in production networks:**
- Set native VLAN to an unused VLAN (not VLAN 1) on all trunks
- Make sure both sides always match
- Tag native VLAN traffic explicitly using `switchport trunk native vlan tag` where supported

---

## Fix Applied

```bash
SW2(config)# interface fa0/1
SW2(config-if)# switchport trunk native vlan 99
```

Matching the native VLAN on SW2 to 99 resolved the mismatch. The CDP warning stopped appearing and both switches showed `Trunking Native Mode VLAN: 99` in `show interfaces fa0/1 switchport`.

---

## Verification After Fix

```bash
SW1# show interfaces fa0/1 switchport
SW2# show interfaces fa0/1 switchport
```

Both output:
```
Trunking Native Mode VLAN: 99
```

Mismatch resolved.
