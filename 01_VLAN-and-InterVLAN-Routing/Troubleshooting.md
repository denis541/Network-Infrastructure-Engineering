# Troubleshooting — VLAN and InterVLAN Routing

## Issue: VLANs not routing between each other

**Symptom:**  
Pings between PC0/PC1 (VLAN 10) and PC2/PC3 (VLAN 20) were failing even 
after subinterfaces were configured on the router.

**Root Cause:**  
The VLANs were assigned IP addresses directly on the switch (SVI interfaces), 
which conflicted with the router subinterfaces. Both the switch SVIs and the 
router subinterfaces were competing to be the default gateway for each VLAN, 
causing routing to break.

**Fix:**  
Removed the IP addresses from the switch VLAN interfaces and kept the default 
gateway role exclusively on the router subinterfaces (Gig0/0/0.10 and 
Gig0/0/0.20). The switch was left to handle only Layer 2 switching with the 
trunk port (Fa0/5) carrying all VLANs to the router.

**Verification:**  
Confirmed successful pings between PC0 and PC2 (across VLANs) after the fix.
