# Troubleshooting — VLSM Subnetting Lab

## Issue: No issues encountered

The lab was completed successfully without major troubleshooting steps.
Connectivity between all VLANs was confirmed via ping after configuring
the router subinterfaces and trunk ports on Switch0.

## Verification Steps Performed

1. Confirmed subinterface configuration using `show running-config`
2. Confirmed trunk port on Switch0 Fa0/1 using `show interfaces trunk`
3. Pinged across VLANs to verify inter-VLAN routing was working
4. Verified correct subnet masks on each PC matched the VLSM design
