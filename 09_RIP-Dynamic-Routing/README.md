# 09 — RIP Dynamic Routing
![Cisco Packet Tracer](https://img.shields.io/badge/Cisco%20Packet%20Tracer-1BA0D7?style=flat&logo=cisco&logoColor=white)
![Cisco IOS](https://img.shields.io/badge/Cisco%20IOS-1BA0D7?style=flat&logo=cisco&logoColor=white)
![RIPv2](https://img.shields.io/badge/Protocol-RIPv2-orange?style=flat)
![Serial WAN](https://img.shields.io/badge/Link-Serial%20WAN-grey?style=flat)
![CCNA](https://img.shields.io/badge/CCNA-Study%20Lab-blue?style=flat)

## Overview

This lab demonstrates RIPv2 (Routing Information Protocol version 2) configured across three routers connected via serial WAN links. The goal is to show how dynamic routing allows routers to automatically learn about networks they are not directly connected to, without manually entering static routes.

RIP is a distance-vector routing protocol that uses hop count as its metric. Each router shares its routing table with its neighbors every 30 seconds. The maximum hop count is 15 — anything beyond that is considered unreachable.

---

## Topology

```
PC1 --- Switch(R1 LAN) --- R1 ---[Se DCE]--- R2 ---[Se DCE]--- R3 --- Switch(R3 LAN) --- PC3
                                                    |
                                             Switch(R2 LAN)
                                                    |
                                                   PC2
```

- R1 connects to R2 via Serial link — R1 is DCE
- R2 connects to R3 via Serial link — R2 is DCE
- Each router has a LAN connected via FastEthernet

---

## Addressing Table

| Device | Interface   | IP Address      | Subnet Mask       | Default Gateway |
|--------|-------------|-----------------|-------------------|-----------------|
| R1     | Fa0/0       | 192.168.1.1     | 255.255.255.0     | —               |
| R1     | Se0/0/0     | 192.168.2.1     | 255.255.255.0     | —               |
| R2     | Fa0/0       | 192.168.3.1     | 255.255.255.0     | —               |
| R2     | Se0/0/0     | 192.168.2.2     | 255.255.255.0     | —               |
| R2     | Se0/0/1     | 192.168.4.2     | 255.255.255.0     | —               |
| R3     | Fa0/0       | 192.168.5.1     | 255.255.255.0     | —               |
| R3     | Se0/0/1     | 192.168.4.1     | 255.255.255.0     | —               |
| PC1    | NIC         | 192.168.1.10    | 255.255.255.0     | 192.168.1.1     |
| PC2    | NIC         | 192.168.3.10    | 255.255.255.0     | 192.168.3.1     |
| PC3    | NIC         | 192.168.5.10    | 255.255.255.0     | 192.168.5.1     |

---

## Key Concepts

### Why RIP is needed here
Each router only knows about the networks directly connected to its own interfaces. Without a routing protocol, R1 has no way to know that 192.168.5.0/24 exists on R3's side. RIP solves this by having routers share their routing tables with neighbors, so every router eventually builds a complete picture of the network.

### Serial interfaces and DCE/DTE
Serial links require one side to provide a clock signal to synchronize communication. The side that provides this signal is called DCE (Data Communications Equipment). The other side is DTE (Data Terminal Equipment). Only the DCE side needs the `clock rate` command — without it the serial link stays down even if both ends have IP addresses configured.

To check which side is DCE:
```
show controllers se0/0/0
```

### The network command in RIP
The `network` command does not assign an IP address. It tells RIP to look at existing interface IPs and activate on any interface whose IP falls within that range. It also advertises that network to RIP neighbors. If an interface is not covered by a `network` statement, RIP will not send or receive updates through it.

### no auto-summary
By default RIPv2 summarizes subnets back to their classful boundary when advertising them. For example 192.168.1.0/24 and 192.168.5.0/24 would both be summarized to 192.168.0.0/16, causing incorrect routing. The `no auto-summary` command disables this and ensures RIP advertises exact subnets as configured.

### Hop count metric
RIP uses hop count to measure distance. Each router in the path counts as one hop. In this topology R1 is two hops from R3's LAN (R1 → R2 → R3), which shows in the routing table as [120/2]. The 120 is RIP's administrative distance and the 2 is the hop count.

---

## Router Configuration

### R1
```
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# interface fa0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface se0/0/0
R1(config-if)# ip address 192.168.2.1 255.255.255.0
R1(config-if)# clock rate 64000
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# router rip
R1(config-router)# version 2
R1(config-router)# network 192.168.1.0
R1(config-router)# network 192.168.2.0
R1(config-router)# no auto-summary
R1(config-router)# end
```

### R2
```
Router> enable
Router# configure terminal
Router(config)# hostname R2
R2(config)# interface fa0/0
R2(config-if)# ip address 192.168.3.1 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# interface se0/0/0
R2(config-if)# ip address 192.168.2.2 255.255.255.0
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# interface se0/0/1
R2(config-if)# ip address 192.168.4.2 255.255.255.0
R2(config-if)# clock rate 64000
R2(config-if)# no shutdown
R2(config-if)# exit
R2(config)# router rip
R2(config-router)# version 2
R2(config-router)# network 192.168.2.0
R2(config-router)# network 192.168.3.0
R2(config-router)# network 192.168.4.0
R2(config-router)# no auto-summary
R2(config-router)# end
```

### R3
```
Router> enable
Router# configure terminal
Router(config)# hostname R3
R3(config)# interface fa0/0
R3(config-if)# ip address 192.168.5.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit
R3(config)# interface se0/0/1
R3(config-if)# ip address 192.168.4.1 255.255.255.0
R3(config-if)# no shutdown
R3(config-if)# exit
R3(config)# router rip
R3(config-router)# version 2
R3(config-router)# network 192.168.4.0
R3(config-router)# network 192.168.5.0
R3(config-router)# no auto-summary
R3(config-router)# end
```

### Device hardening (all routers)
```
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# enable secret cisco
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# service password-encryption
```

---

## Verification

### show ip route on R1
```
Gateway of last resort is not set

C    192.168.1.0/24 is directly connected, FastEthernet0/0
C    192.168.2.0/24 is directly connected, Serial0/0/0
R    192.168.3.0/24 [120/1] via 192.168.2.2, 00:00:12, Serial0/0/0
R    192.168.4.0/24 [120/1] via 192.168.2.2, 00:00:12, Serial0/0/0
R    192.168.5.0/24 [120/2] via 192.168.2.2, 00:00:12, Serial0/0/0
```

R1 learned three networks via RIP. 192.168.5.0/24 shows a hop count of 2 because it is two routers away — R1 must go through R2 to reach R3.

### show ip route on R3
```
Gateway of last resort is not set

R    192.168.1.0/24 [120/2] via 192.168.4.2, 00:00:11, Serial0/0/1
R    192.168.2.0/24 [120/1] via 192.168.4.2, 00:00:11, Serial0/0/1
R    192.168.3.0/24 [120/1] via 192.168.4.2, 00:00:11, Serial0/0/1
C    192.168.4.0/24 is directly connected, Serial0/0/1
C    192.168.5.0/24 is directly connected, FastEthernet0/0
```

R3 mirrors R1 — it learned R1's LAN as 2 hops away and R2's networks as 1 hop away.

### End-to-end ping — PC1 to PC3
```
Pinging 192.168.5.10 with 32 bytes of data:

Reply from 192.168.5.10: bytes=32 time=10ms TTL=125
Reply from 192.168.5.10: bytes=32 time=16ms TTL=125
Reply from 192.168.5.10: bytes=32 time=15ms TTL=125
Reply from 192.168.5.10: bytes=32 time=15ms TTL=125

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

---

## Tools Used

- Cisco Packet Tracer
- Cisco IOS CLI

## Protocols and Commands

- RIPv2
- `router rip` / `version 2` / `network` / `no auto-summary`
- `clock rate` on DCE serial interfaces
- `show ip route` / `show ip interface brief` / `show controllers`
- `enable secret` / `service password-encryption` / `line console` / `line vty`
