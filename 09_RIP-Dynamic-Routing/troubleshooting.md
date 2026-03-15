# Troubleshooting Notes — RIP 3-Router Lab

Real issues encountered during this lab and how they were resolved.

---

## Issue 1 — RIP learned a summarized route instead of a specific subnet

### Symptom
After configuring RIP on all three routers, R1's routing table showed an unexpected entry:

```
R    172.16.0.0/16 [120/1] via 192.168.2.2, Serial0/0/0
```

Instead of the expected specific subnet, RIP was advertising a summarized classful route.

### Cause
`no auto-summary` was missing on one of the routers. RIPv2 still has auto-summary enabled by default, which causes it to summarize subnets back to their classful boundary before advertising them to neighbors. Two subnets from the same classful range were being collapsed into one vague route, which would cause misrouting in a real network.

### Fix
```
R2(config)# router rip
R2(config-router)# no auto-summary
```

### Lesson
Always include `no auto-summary` when configuring RIPv2. Without it, RIP loses subnet mask information during advertisements and the routing table becomes inaccurate.

---

## Issue 2 — Serial interface stayed administratively down

### Symptom
After configuring the serial interface with an IP address, `show ip interface brief` showed:

```
Serial0/0/0   192.168.2.1   YES   manual   administratively down   down
```

### Cause
The `no shutdown` command was not run on the interface. Cisco router interfaces are disabled by default and must be explicitly brought up.

### Fix
```
R1(config)# interface se0/0/0
R1(config-if)# no shutdown
```

### Lesson
Always run `no shutdown` after assigning an IP to an interface. A common habit is to include it as the last line of every interface configuration block before exiting.

---

## Issue 3 — Serial link showed up/down after no shutdown

### Symptom
After running `no shutdown` on the serial interface, the status showed:

```
Serial0/0/0   192.168.2.1   YES   manual   up   down
```

The interface came up on the physical layer but the line protocol stayed down.

### Cause
The DCE side of the serial link was missing the `clock rate` command. Serial links require one side to provide a clock signal. Without it the link cannot synchronize and the protocol layer stays down even if the physical connection is present.

### Fix
First confirmed which side was DCE:
```
show controllers se0/0/0
```
Output confirmed DCE, then applied clock rate:
```
R1(config)# interface se0/0/0
R1(config-if)# clock rate 64000
```

### Lesson
Whenever a serial link shows `up/down`, check `show controllers` on both ends. The DCE side must have `clock rate` set. This is only relevant for serial interfaces — FastEthernet and GigabitEthernet handle clocking automatically.

---

## Issue 4 — Same IP address configured on two different serial interfaces

### Symptom
`show ip interface brief` on R1 showed the same IP address on both Se0/0/0 and Se0/1/0:

```
Serial0/0/0   10.10.12.1   YES   manual   administratively down   down
Serial0/1/0   10.10.12.1   YES   manual   down                    down
```

### Cause
The IP address was configured on the wrong interface first (Se0/1/0), then configured again on the correct interface (Se0/0/0) without removing it from the first one.

### Fix
Removed the IP from the incorrect interface:
```
R1(config)# interface se0/1/0
R1(config-if)# no ip address
R1(config-if)# shutdown
R1(config-if)# exit
```

### Lesson
Before configuring an interface, run `show ip interface brief` to confirm which physical port your cable is connected to. Configuring the wrong interface is a common mistake, especially on routers with multiple serial slots.

---

## Useful Verification Commands

| Command | Purpose |
|---|---|
| `show ip interface brief` | Quick status of all interfaces |
| `show ip route` | Full routing table including RIP-learned routes |
| `show controllers se0/0/0` | Check if interface is DCE or DTE |
| `show run | section router rip` | Verify RIP configuration |
| `show ip rip database` | RIP's internal route database |
| `debug ip rip` | Live view of RIP updates (use carefully, disable with `undebug all`) |
