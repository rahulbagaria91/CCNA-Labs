# Inter-VLAN Routing Lab

A hands-on Cisco Packet Tracer lab demonstrating **VLAN segmentation, 802.1Q trunking, and Router-on-a-Stick inter-VLAN routing**.

> **Status:** Completed  
> **Platform:** Cisco Packet Tracer  
> **Focus:** CCNA Switching & Routing

---

## Overview

This lab builds a small enterprise-style LAN with **2 Cisco switches, 1 router, and 7 end devices**.

The objective is to separate users into different VLANs and then configure the router to provide communication between those VLANs.

### Concepts demonstrated

- VLAN creation and naming
- Access-port configuration
- 802.1Q trunking
- Router-on-a-Stick
- Inter-VLAN routing
- IPv4 addressing
- Default gateways
- MAC address learning
- ARP resolution
- ICMP/Ping verification
- Cisco IOS verification commands
- Packet Tracer Simulation Mode

---

## Network Topology

```text
                         ┌──────────────┐
                         │     R1       │
                         │   Router     │
                         │ Gi0/0 Gi0/1  │
                         └──────┬───────┘
                                │
                    802.1Q Trunk Links
                         │              │
                  ┌──────┴──────┐  ┌────┴───────┐
                  │    SW1      │  │    SW2     │
                  │  VLAN 10/20 │  │  VLAN 30/40│
                  └──┬─┬─┬─┬────┘  └──┬─┬─┬────┘
                     │ │ │ │           │ │ │
                    PC PC PC PC       PC PC PC
                    0  1  2  3        5  6  7

                         7 End Devices
```

---

## Lab Architecture

| Device | Role | Key Interfaces |
|---|---|---|
| R1 | Inter-VLAN Router | Gi0/0, Gi0/1 |
| SW1 | Access Switch | Fa0/1–Fa0/4, Fa0/5 trunk |
| SW2 | Access Switch | Fa0/1–Fa0/3, Fa0/4 trunk |
| PC0–PC3 | End Devices | Fa0 |
| PC5–PC7 | End Devices | Fa0 |

---

## VLAN & IP Design

| VLAN | Purpose | Network | Default Gateway |
|---:|---|---|---|
| 10 | Users | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Admin | 192.168.20.0/24 | 192.168.20.1 |
| 30 | IT | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Cyber | 192.168.40.0/24 | 192.168.40.1 |

> The lab contains **7 end devices** distributed across the configured VLANs. Individual host addresses are assigned from their corresponding `/24` network.

---

## Router Configuration

The router uses **subinterfaces** to provide a Layer 3 gateway for each VLAN.

Example configuration:

```cisco
enable
configure terminal

hostname R1

interface gigabitEthernet 0/0
 no shutdown

interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface gigabitEthernet 0/1
 no shutdown

interface gigabitEthernet 0/1.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface gigabitEthernet 0/1.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0

end
```

### Why subinterfaces?

A single physical router interface can carry traffic for multiple VLANs when 802.1Q tagging is used.

For example:

```text
Gi0/0.10 → VLAN 10 → 192.168.10.1
Gi0/0.20 → VLAN 20 → 192.168.20.1

Gi0/1.30 → VLAN 30 → 192.168.30.1
Gi0/1.40 → VLAN 40 → 192.168.40.1
```

---

## Switch Configuration

### VLAN creation

Example on SW1:

```cisco
enable
configure terminal

vlan 10
 name Users

vlan 20
 name Admin

end
```

Example on SW2:

```cisco
enable
configure terminal

vlan 30
 name IT

vlan 40
 name Cyber

end
```

### Access ports

Example:

```cisco
interface range fa0/1-2
 switchport mode access
 switchport access vlan 10
```

And:

```cisco
interface range fa0/3-4
 switchport mode access
 switchport access vlan 20
```

### Trunk configuration

SW1:

```cisco
interface fa0/5
 switchport mode trunk
```

SW2:

```cisco
interface fa0/4
 switchport mode trunk
```

The trunk carries multiple VLANs using **IEEE 802.1Q tagging**.

---

## End Device Configuration

Each PC/laptop is configured with:

```text
IP Address
Subnet Mask: 255.255.255.0
Default Gateway: VLAN gateway
```

Example for a VLAN 10 host:

```text
IP Address:      192.168.10.x
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

Example for a VLAN 20 host:

```text
IP Address:      192.168.20.x
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.20.1
```

The same principle applies to VLAN 30 and VLAN 40.

---

## Verification

### 1. Verify VLANs

```cisco
show vlan brief
```

Expected result:

```text
10  Users   active
20  Admin   active
30  IT      active
40  Cyber   active
```

---

### 2. Verify trunking

```cisco
show interfaces trunk
```

Important fields:

```text
Mode          on
Encapsulation 802.1q
Status        trunking
```

---

### 3. Verify router interfaces

```cisco
show ip interface brief
```

The configured subinterfaces should show:

```text
up    up
```

---

### 4. Verify routing table

```cisco
show ip route
```

The router should learn the VLAN networks as directly connected networks:

```text
C 192.168.10.0/24
C 192.168.20.0/24
C 192.168.30.0/24
C 192.168.40.0/24
```

---

### 5. Verify MAC address learning

```cisco
show mac address-table
```

The switch should dynamically learn end-device MAC addresses on the appropriate access ports.

---

### 6. Test connectivity

From a PC:

```text
ping <same-VLAN-host>
```

Then test inter-VLAN communication:

```text
ping <host-in-another-VLAN>
```

Successful replies confirm that the router is performing inter-VLAN routing.

---

## ARP & ICMP Observation

Packet Tracer Simulation Mode was also used to observe traffic.

The lab demonstrates the relationship between:

```text
ARP
 ↓
MAC Address Resolution
 ↓
Ethernet Frame
 ↓
Router / Layer 3 Forwarding
 ↓
ICMP Echo Request
 ↓
ICMP Echo Reply
```

This provides a practical view of what happens beyond simply running `ping`.

---

## Useful Cisco IOS Commands

| Command | Purpose |
|---|---|
| `show vlan brief` | Display VLANs and assigned ports |
| `show interfaces trunk` | Verify trunk links |
| `show mac address-table` | Display learned MAC addresses |
| `show ip interface brief` | Check interface status/IPs |
| `show ip route` | Display routing table |
| `show running-config` | View active configuration |
| `show startup-config` | View saved configuration |
| `show cdp neighbors` | Discover directly connected Cisco devices |
| `copy running-config startup-config` | Save configuration |

---

## Troubleshooting Checklist

If an inter-VLAN ping fails, check in this order:

```text
1. PC IP address
2. Subnet mask
3. Default gateway
4. VLAN assignment
5. Access-port configuration
6. Trunk status
7. 802.1Q VLAN ID
8. Router subinterface IP
9. Router interface status
10. Routing table
```

Useful commands:

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show mac address-table
```

---

## Evidence

Recommended screenshots included with this lab:

| Evidence | What it demonstrates |
|---|---|
| Topology | Complete 7-endpoint network |
| VLAN table | VLAN creation and port assignment |
| Trunk verification | 802.1Q trunk operation |
| Router interfaces | Subinterfaces and gateway IPs |
| Routing table | Directly connected VLAN networks |
| MAC table | Dynamic MAC learning |
| Ping results | Successful connectivity |
| Simulation Mode | ARP/ICMP packet flow |

---

## Project Files

```text
Inter-VLAN-Routing-Lab/
│
├── README.md
├── Inter-VLAN-Routing-Lab.pkt
│
└── screenshots/
    ├── 01-topology.png
    ├── 02-vlan-configuration.png
    ├── 03-sw1-trunk.png
    ├── 04-sw2-trunk.png
    ├── 05-router-interfaces.png
    ├── 06-routing-table.png
    ├── 07-mac-address-table.png
    ├── 08-ping-test.png
    └── 09-simulation-arp-icmp.png
```

---

## Key Learning Outcomes

After completing this lab, the following concepts were practiced hands-on:

- Layer 2 VLAN segmentation
- Access vs trunk ports
- IEEE 802.1Q
- Router-on-a-Stick
- Layer 3 inter-VLAN communication
- IPv4 addressing
- Default gateways
- MAC address learning
- ARP
- ICMP
- Cisco IOS verification
- Basic network troubleshooting
- Packet-level analysis in Cisco Packet Tracer

---

## Lab Status

- [x] Topology created
- [x] VLANs configured
- [x] Access ports configured
- [x] Trunk ports configured
- [x] Router subinterfaces configured
- [x] End devices configured
- [x] Inter-VLAN routing tested
- [x] ARP/ICMP observed in Simulation Mode
- [x] Configuration saved

---

## Next Improvements

Possible future extensions:

- DHCP server configuration
- SSH remote management
- Switch management VLAN
- Port security
- STP configuration and analysis
- EtherChannel
- ACLs
- Dynamic routing
- Network security hardening

---

## Author

**Rahul Bagaria**

B.Tech Computer Science & Engineering  
CCNA / Networking Hands-on Labs

GitHub: [rahulbagaria91](https://github.com/rahulbagaria91)

---

> This project was created as part of a hands-on CCNA networking practice portfolio using Cisco Packet Tracer.
