# 10 — SSH Remote Access Configuration
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7?style=flat&logo=cisco&logoColor=white) ![CCNA 200-301](https://img.shields.io/badge/CCNA-200--301-00BCEB?style=flat&logo=cisco&logoColor=white) ![Protocol](https://img.shields.io/badge/Protocol-SSH%20v2-4CAF50?style=flat) ![VLAN](https://img.shields.io/badge/VLAN-Management-8B5CF6?style=flat) ![Routing](https://img.shields.io/badge/Routing-Static-F59E0B?style=flat) ![Status](https://img.shields.io/badge/Status-Complete-22C55E?style=flat)

## Objectives
- Configure SSH on routers and switches from scratch
- Create local user accounts for authenticated remote access
- Enforce SSH version 2 and disable Telnet on VTY lines 
- Configure a management VLAN (VLAN 10) on a switch for secure access
- Assign IPs to router interfaces and a switch SVI
- Add a static route to enable end-to-end reachability
- Verify SSH connectivity from a PC to all network devices

---

## Topology

```
PC1 ── Fa0/1 ──┐
               SW1 (Gi0/1) ── (Gi0/0/1) R1 (Gi0/0/0) ── (Gi0/0/0) R2
PC2 ── Fa0/2 ──┘
```

---

## Addressing Table

| Device | Interface    | IP Address      | Subnet Mask         | Default Gateway |
|--------|--------------|-----------------|---------------------|-----------------|
| R1     | Gi0/0/0      | 10.0.0.1        | 255.255.255.252      | —               |
| R1     | Gi0/0/1      | 192.168.1.1     | 255.255.255.0        | —               |
| R2     | Gi0/0/0      | 10.0.0.2        | 255.255.255.252      | —               |
| SW1    | VLAN 10      | 192.168.1.254   | 255.255.255.0        | 192.168.1.1     |
| PC1    | NIC          | 192.168.1.10    | 255.255.255.0        | 192.168.1.1     |
| PC2    | NIC          | 192.168.1.20    | 255.255.255.0        | 192.168.1.1     |

---

## VLAN Table

| VLAN | Name       | Ports Assigned       |
|------|------------|----------------------|
| 10   | MANAGEMENT | Fa0/1, Fa0/2, Gi0/1  |

---

## Device Configurations

### R1

```
hostname R1
ip domain-name netlab.local
username netadmin privilege 15 secret Admin@123
crypto key generate rsa
! When prompted enter: 1024
ip ssh version 2

interface GigabitEthernet0/0/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/0/1
 ip address 192.168.1.1 255.255.255.0
 no shutdown

line vty 0 15
 login local
 transport input ssh
 exec-timeout 5 0
```

---

### R2

```
hostname R2
ip domain-name netlab.local
username netadmin privilege 15 secret Admin@123
crypto key generate rsa
! When prompted enter: 1024
ip ssh version 2

interface GigabitEthernet0/0/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown

ip route 192.168.1.0 255.255.255.0 10.0.0.1

line vty 0 15
 login local
 transport input ssh
 exec-timeout 5 0
```

---

### SW1

```
hostname SW1
ip domain-name netlab.local
username netadmin privilege 15 secret Admin@123
crypto key generate rsa
! When prompted enter: 1024
ip ssh version 2

vlan 10
 name MANAGEMENT

interface Fa0/1
 switchport mode access
 switchport access vlan 10

interface Fa0/2
 switchport mode access
 switchport access vlan 10

interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
 no shutdown

interface vlan 10
 ip address 192.168.1.254 255.255.255.0
 no shutdown

ip default-gateway 192.168.1.1

line vty 0 15
 login local
 transport input ssh
 exec-timeout 5 0
```

---

## Verification

### Check SSH status on all devices
```
show ip ssh
show ssh
```

Expected output:
```
SSH Enabled - version 2.0
Authentication timeout: 120 secs; Authentication retries: 3
```

### Check routing table on R1
```
show ip route
```
Expected: Two directly connected networks (`10.0.0.0/30` and `192.168.1.0/24`)

### Check routing table on R2
```
show ip route
```
Expected: Static route `S 192.168.1.0/24 [1/0] via 10.0.0.1`

### Check VLAN assignments on SW1
```
show vlan brief
```
Expected: VLAN 10 MANAGEMENT active with Fa0/1, Fa0/2, Gi0/1

### SSH connectivity tests
| Source | Destination     | Command                          | Expected Result |
|--------|-----------------|----------------------------------|-----------------|
| PC1    | R1              | `ssh -l netadmin 192.168.1.1`    | Login success   |
| PC1    | SW1             | `ssh -l netadmin 192.168.1.254`  | Login success   |
| PC1    | R2              | `ssh -l netadmin 10.0.0.2`       | Login success   |
| R1     | R2              | `ssh -l netadmin 10.0.0.2`       | Login success   |

---

## Key Concepts

**VTY Lines**
Virtual Teletype lines are software ports that handle remote access (SSH/Telnet). `line vty 0 15` configures 16 simultaneous remote sessions. Setting `login local` enforces username/password authentication and `transport input ssh` blocks Telnet entirely.

**RSA Keys**
SSH encrypts all traffic between the client and the device using RSA key pairs. The public key is shared during the handshake and the private key stays on the device. Without the RSA key, SSH cannot function. The hostname and domain name are required before key generation as they are used to label the key pair (e.g. `R1.netlab.local`).

**SVI (Switched Virtual Interface)**
Switches operate at Layer 2 and cannot assign IPs to physical ports. The SVI (`interface vlan X`) is a virtual Layer 3 interface that gives the switch an IP address for management purposes. Using a dedicated management VLAN (VLAN 10) instead of the default VLAN 1 is a security best practice.

**Static Route on R2**
R2 needed a static route to reach the `192.168.1.0/24` network because it has no direct connection to it. Without this route, R2 had no path to send return traffic back to PC1, causing pings and SSH to fail.

**Principle of Least Privilege**
User accounts can be assigned privilege levels (1–15). Privilege 15 gives full access while privilege 1 is read-only. In production networks different users are given different privilege levels based on their role.

---

## Files in This Lab

| File | Description |
|------|-------------|
| `README.md` | Full lab documentation (this file) |
| `ssh-lab.pkt` | Packet Tracer topology file |
| `configs/R1.txt` | Running configuration for R1 |
| `configs/R2.txt` | Running configuration for R2 |
| `configs/SW1.txt` | Running configuration for SW1 |

---

## Notes
- Packet Tracer does not support `crypto key generate rsa modulus 2048` directly — use `crypto key generate rsa` and enter the modulus size when prompted
- The `/30` subnet (`255.255.255.252`) between R1 and R2 is a point-to-point link with only 2 usable IPs — this is standard practice for router-to-router links
- `exec-timeout 5 0` automatically disconnects idle SSH sessions after 5 minutes — good security practice

---

*Lab completed as part of CCNA 200-301 exam preparation*
