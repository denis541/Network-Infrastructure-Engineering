# — Voice VLAN

![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-blue?style=flat-square&logo=cisco)
![VLANs](https://img.shields.io/badge/VLANs-Data%20%7C%20Voice-orange?style=flat-square)
![Protocol](https://img.shields.io/badge/Protocol-802.1Q-yellow?style=flat-square)
![CDP](https://img.shields.io/badge/CDP-Enabled-informational?style=flat-square)
![CCNA](https://img.shields.io/badge/CCNA-Aligned-red?style=flat-square)

## Objective

Configure a switch access port to carry both a data VLAN and a voice VLAN simultaneously over a single cable. This mirrors real office deployments where one cable drop serves both an IP phone and a PC connected behind it.

---

## Topology

```
[2960 Switch]
     |
   Fa0/1
     |
[IP Phone] ← Voice VLAN 30 (tagged, 802.1Q)
     |
   Fa0/0
     |
  [PC0]    ← Data VLAN 10 (untagged)
```

---

## VLAN Configuration

| VLAN | Name  | Type  | Traffic        |
|------|-------|-------|----------------|
| 10   | Data  | Data  | PC0            |
| 30   | Voice | Voice | IP Phone       |

---

## IP Addressing Table

| Device   | IP Address   | Subnet Mask   | Default Gateway |
|----------|--------------|---------------|-----------------|
| PC0      | 192.168.10.2 | 255.255.255.0 | 192.168.10.1    |
| IP Phone | Auto via CDP | —             | —               |

---

## Configuration

### Step 1 — Create VLANs

```bash
SW(config)# vlan 10
SW(config-vlan)# name Data
SW(config)# vlan 30
SW(config-vlan)# name Voice
```

### Step 2 — Configure the Access Port

```bash
SW(config)# interface fa0/1
SW(config-if)# switchport mode access
SW(config-if)# switchport access vlan 10
SW(config-if)# switchport voice vlan 30
SW(config-if)# no shutdown
```

The `switchport access vlan 10` carries untagged PC traffic. The `switchport voice vlan 30` carries 802.1Q tagged voice traffic from the IP phone — both over the same physical cable.

---

## Verification Commands

```bash
# Confirm both VLANs on the port
SW# show interfaces fa0/1 switchport

# Confirm VLANs exist
SW# show vlan brief
```

### Expected Output — show interfaces fa0/1 switchport

```
Access Mode VLAN: 10 (Data)
Voice VLAN: 30 (Voice)
```

---

## How It Works

A single switch port in voice VLAN mode operates differently from a standard access port. It carries two types of traffic simultaneously:

- **PC traffic (VLAN 10)** — sent untagged, exactly like a normal access port
- **Phone traffic (VLAN 30)** — sent with an 802.1Q tag so the switch knows it belongs to the voice VLAN

The IP phone learns which VLAN to tag its traffic with via **CDP (Cisco Discovery Protocol)**. The switch sends a CDP advertisement to the phone telling it to use VLAN 30 for voice traffic. No manual configuration is needed on the phone itself.

The PC plugged into the back of the phone has no awareness of any of this — it simply sees a normal network connection on VLAN 10.

---

## Why Voice Gets Its Own VLAN

Voice traffic is extremely sensitive to delay, jitter, and packet loss. Separating it into its own VLAN allows the network to apply **QoS (Quality of Service)** policies that prioritize voice packets over regular data traffic. Without this separation, a large file download on the data VLAN could degrade call quality on the same link.

---

## Concepts Covered

- Voice VLAN — a dedicated VLAN for IP phone traffic on an access port
- `switchport voice vlan` — configures the auxiliary VLAN for tagged voice traffic
- CDP — used by the switch to automatically tell the IP phone which VLAN to use
- 802.1Q tagging on an access port — voice traffic is tagged, data traffic is untagged, both on one port
- QoS — the primary reason voice traffic is kept in a separate VLAN
