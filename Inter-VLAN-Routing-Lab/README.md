# 🌐 Inter-VLAN Routing Lab

![Cisco](https://img.shields.io/badge/Cisco-Packet%20Tracer-1BA0D7)
![CCNA](https://img.shields.io/badge/CCNA-200--301-red)
![Networking](https://img.shields.io/badge/Networking-Inter--VLAN%20Routing-blue)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## 📖 Overview

This lab demonstrates **Inter-VLAN Routing using Router-on-a-Stick** in Cisco Packet Tracer.

The network was built as a hands-on CCNA lab using:

- 1 Cisco Router
- 2 Cisco Switches
- 7 End Devices
- Multiple VLANs
- 802.1Q trunking
- Router subinterfaces
- IPv4 addressing
- ARP and ICMP verification

The main goal was to understand how devices in **different VLANs communicate through a Layer 3 router**.

---

## 🎯 Objectives

- Create and configure VLANs
- Assign switch access ports to VLANs
- Configure 802.1Q trunk links
- Configure Router-on-a-Stick
- Create router subinterfaces
- Configure default gateways
- Enable communication between VLANs
- Verify MAC address learning
- Observe ARP and ICMP packet flow
- Troubleshoot connectivity using Cisco IOS commands
- Save and verify the final configuration

---

## 🖧 Network Topology

```text
                              ┌──────────────┐
                              │      R1      │
                              │    Router    │
                              │ Gi0/0  Gi0/1 │
                              └──────┬──┬────┘
                                     │  │
                           802.1Q    │  │    802.1Q
                           Trunk     │  │    Trunk
                                     │  │
                    ┌────────────────┘  └────────────────┐
                    │                                    │
              ┌─────┴─────┐                        ┌────┴─────┐
              │    SW1    │                        │   SW2    │
              │ VLAN 10/20│                        │ VLAN 30/40│
              └─┬─┬─┬─┬───┘                        └─┬─┬─┬────┘
                │ │ │ │                              │ │ │
               PC0 PC1 PC2 PC3                      PC5 PC6 PC7
```

### Device Summary

| Device | Role | Connections |
|---|---|---|
| R1 | Inter-VLAN Router | SW1 + SW2 |
| SW1 | Access Switch | PC0–PC3 + R1 |
| SW2 | Access Switch | PC5–PC7 + R1 |
| PC0 | End Device | SW1 |
| PC1 | End Device | SW1 |
| PC2 | End Device | SW1 |
| PC3 | End Device | SW1 |
| PC5 | End Device | SW2 |
| PC6 | End Device | SW2 |
| PC7 | End Device | SW2 |

---

## 🏷️ VLAN Design

| VLAN | Name | Network | Default Gateway |
|---:|---|---|---|
| 10 | Users | `192.168.10.0/24` | `192.168.10.1` |
| 20 | Admin | `192.168.20.0/24` | `192.168.20.1` |
| 30 | IT | `192.168.30.0/24` | `192.168.30.1` |
| 40 | Cyber | `192.168.40.0/24` | `192.168.40.1` |

> **Note:** The lab contains 7 end devices. Each host is assigned an IPv4 address from the network of its configured VLAN.

---

## 🔀 Switch Configuration

### SW1 — VLANs 10 and 20

```cisco
enable
configure terminal

vlan 10
 name Users

vlan 20
 name Admin

interface range fa0/1-2
 switchport mode access
 switchport access vlan 10

interface range fa0/3-4
 switchport mode access
 switchport access vlan 20

interface fa0/5
 switchport mode trunk

end
```

### SW2 — VLANs 30 and 40

```cisco
enable
configure terminal

vlan 30
 name IT

vlan 40
 name Cyber

interface range fa0/1-2
 switchport mode access
 switchport access vlan 30

interface fa0/3
 switchport mode access
 switchport access vlan 40

interface fa0/4
 switchport mode trunk

end
```

---

## 🚦 Router-on-a-Stick Configuration

The router uses subinterfaces to provide gateways for the VLANs.

### R1

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

### Router Interface Verification

```cisco
show ip interface brief
```

Expected subinterfaces:

```text
GigabitEthernet0/0.10    192.168.10.1    up    up
GigabitEthernet0/0.20    192.168.20.1    up    up
GigabitEthernet0/1.30    192.168.30.1    up    up
GigabitEthernet0/1.40    192.168.40.1    up    up
```

---

## 💻 End Device Configuration

Each PC/laptop was configured with:

```text
IPv4 Address
Subnet Mask: 255.255.255.0
Default Gateway: VLAN gateway
```

Example:

```text
VLAN 10 → 192.168.10.x → Gateway 192.168.10.1
VLAN 20 → 192.168.20.x → Gateway 192.168.20.1
VLAN 30 → 192.168.30.x → Gateway 192.168.30.1
VLAN 40 → 192.168.40.x → Gateway 192.168.40.1
```

---

## 🔍 Verification

### 1. Verify VLANs

```cisco
show vlan brief
```

Confirmed VLANs:

```text
10  Users
20  Admin
30  IT
40  Cyber
```

### 2. Verify Trunks

```cisco
show interfaces trunk
```

Expected:

```text
Encapsulation: 802.1q
Status:        trunking
```

### 3. Verify MAC Address Learning

```cisco
show mac address-table
```

The switch dynamically learns source MAC addresses and associates them with the correct switch ports/VLANs.

### 4. Verify Routing Table

```cisco
show ip route
```

The router should show the four VLAN networks as directly connected:

```text
C 192.168.10.0/24
C 192.168.20.0/24
C 192.168.30.0/24
C 192.168.40.0/24
```

### 5. Verify Connectivity

Inter-VLAN communication was tested using:

```text
ping <destination-IP>
```

Successful ICMP replies confirmed Layer 3 communication between different VLANs.

---

## 🧪 ARP & ICMP — Simulation Mode

Packet Tracer **Simulation Mode** was used to observe how traffic moves through the network.

The lab demonstrates:

```text
Host
  ↓
ARP Request
  ↓
ARP Reply
  ↓
MAC Address Resolution
  ↓
Ethernet Frame
  ↓
Router
  ↓
Inter-VLAN Routing
  ↓
ICMP Echo Request
  ↓
ICMP Echo Reply
```

This helped connect the theoretical concepts of **ARP, Ethernet, IP routing and ICMP** with actual packet flow.

---

## 🛠️ Useful Cisco IOS Commands

| Command | Purpose |
|---|---|
| `show vlan brief` | Verify VLANs and access ports |
| `show interfaces trunk` | Verify trunk links |
| `show mac address-table` | View learned MAC addresses |
| `show ip interface brief` | Check interface status/IP addresses |
| `show ip route` | View routing table |
| `show running-config` | View current configuration |
| `show startup-config` | View saved configuration |
| `show cdp neighbors` | View directly connected Cisco devices |
| `copy running-config startup-config` | Save configuration |

---

## 🖼️ Lab Evidence

Recommended screenshots stored in the project:

```text
screenshots/
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

These screenshots provide evidence of the configuration, verification and packet flow performed during the lab.

---

## 🧠 What I Learned

This lab provided hands-on practice with:

- VLAN segmentation
- Access ports
- Trunk ports
- IEEE 802.1Q
- Router-on-a-Stick
- Router subinterfaces
- Inter-VLAN routing
- IPv4 addressing
- Default gateways
- MAC address learning
- ARP
- ICMP
- Cisco IOS verification
- Basic network troubleshooting
- Packet Tracer Simulation Mode

---

## ✅ Completion Checklist

- [x] Network topology created
- [x] 7 end devices connected
- [x] VLANs created
- [x] Access ports configured
- [x] Trunk links configured
- [x] Router subinterfaces configured
- [x] IP addressing completed
- [x] Default gateways configured
- [x] Inter-VLAN routing tested
- [x] MAC address table verified
- [x] Routing table verified
- [x] ARP/ICMP observed
- [x] Configuration saved

---

## 📁 Project Structure

```text
Inter-VLAN-Routing-Lab/
│
├── README.md
├── Inter-VLAN-Routing-Lab.pkt
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

## 🚀 Future Extensions

This lab can be extended with:

- DHCP
- SSH remote management
- Switch management VLAN
- Port Security
- Spanning Tree Protocol (STP)
- EtherChannel
- Access Control Lists (ACLs)
- Dynamic Routing
- Network Security Hardening

---

## 👨‍💻 Author

**Rahul Bagaria**

B.Tech — Computer Science & Engineering  
CCNA / Networking Hands-on Lab Portfolio

GitHub: **[rahulbagaria91](https://github.com/rahulbagaria91)**

---

> Built as a hands-on CCNA networking lab using Cisco Packet Tracer.
