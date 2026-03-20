# Troubleshooting — Spanning Tree Protocol Lab

## Issue 1: Pings failing across VLANs

**Symptom:**
Pings between PCs on different VLANs were failing completely.

**Root Cause:**
Trunk mode was not enabled on the inter-switch links and the uplink to the
router. Ports were left in default access mode, meaning VLAN traffic could
not pass between switches or reach the router for inter-VLAN routing.

**Fix:**
Configured all inter-switch links and the router uplink (Fa0/24 on Switch1)
as trunk ports:

    interface Fa0/24
     switchport mode trunk

Applied the same configuration to all inter-switch links on Switch1, Switch2,
Switch3, and Switch4.

**Verification:**
Confirmed trunk status using `show interfaces trunk` on each switch.

---

## Issue 2: PCs slow to connect / PortFast not consistent

**Symptom:**
Some PCs were taking a long time to establish connectivity after being
connected. Pings would fail initially and only succeed after waiting through
STP convergence (up to 30 seconds).

**Root Cause:**
PortFast was only enabled on one PC-facing access port. The remaining access
ports connecting to PC1, PC2, and PC3 were going through the full STP
listening and learning states before forwarding, causing the delay.

**Fix:**
Enabled PortFast on all access ports facing end devices:

    interface Fa0/4
     spanning-tree portfast

Applied to the relevant access port on Switch1, Switch2, Switch3, and Switch4.

**Verification:**
Confirmed immediate port forwarding on PC connections after enabling PortFast.
Verified using `show spanning-tree interface Fa0/4 detail`.
