# Inter-VLAN Routing Lab

A hands-on Cisco Packet Tracer lab demonstrating VLAN segmentation,
802.1Q trunking, Router-on-a-Stick, inter-VLAN routing, ARP, MAC address
learning, and ICMP connectivity.

------------------------------------------------------------------------

## Overview

This lab simulates a small enterprise network using:

-   1 × Cisco 1941 Router
-   2 × Cisco 2960 Switches
-   8 × End Devices
-   4 × VLANs
-   802.1Q trunk links
-   Router subinterfaces
-   Inter-VLAN routing

The goal is to understand how devices in different VLANs communicate
through a router.

------------------------------------------------------------------------

## Network Topology

``` text
                         +-------------+
                         |     R1      |
                         |   Router    |
                         +------+------+
                                |
                    +-----------+-----------+
                    |                       |
                 Trunk                    Trunk
                    |                       |
              +-----+-----+           +-----+-----+
              |    SW1    |           |    SW2    |
              +--+--+--+--+           +--+--+--+--+
                 |  |  |                  |  |  |
               VLAN10 VLAN20            VLAN30 VLAN40
```

------------------------------------------------------------------------

## VLAN & IP Addressing

    VLAN Name    Network             Default Gateway
  ------ ------- ------------------- -----------------
      10 Users   `192.168.10.0/24`   `192.168.10.1`
      20 Admin   `192.168.20.0/24`   `192.168.20.1`
      30 IT      `192.168.30.0/24`   `192.168.30.1`
      40 Cyber   `192.168.40.0/24`   `192.168.40.1`

------------------------------------------------------------------------

## Switch Port Assignment

### SW1

  Port    Purpose          VLAN
  ------- ------------- -------
  Fa0/1   End Device         10
  Fa0/2   End Device         10
  Fa0/3   End Device         20
  Fa0/4   End Device         20
  Fa0/5   Trunk to R1     Trunk

### SW2

  Port    Purpose          VLAN
  ------- ------------- -------
  Fa0/1   End Device         30
  Fa0/2   End Device         30
  Fa0/3   End Device         40
  Fa0/4   Trunk to R1     Trunk

------------------------------------------------------------------------

## Router-on-a-Stick Configuration

R1 uses subinterfaces to provide Layer 3 gateways for the VLANs.

``` text
GigabitEthernet0/0.10 → 192.168.10.1/24
GigabitEthernet0/0.20 → 192.168.20.1/24
GigabitEthernet0/1.30 → 192.168.30.1/24
GigabitEthernet0/1.40 → 192.168.40.1/24
```

Each subinterface uses IEEE 802.1Q encapsulation for its corresponding
VLAN.

------------------------------------------------------------------------

## Key Configuration Concepts

### Create VLANs

``` cisco
vlan 10
name Users

vlan 20
name Admin
```

### Configure an Access Port

``` cisco
interface fa0/1
switchport mode access
switchport access vlan 10
```

### Configure a Trunk

``` cisco
interface fa0/5
switchport mode trunk
```

### Configure a Router Subinterface

``` cisco
interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
```

------------------------------------------------------------------------

## Verification Commands

### VLANs

``` cisco
show vlan brief
```

### Trunk Status

``` cisco
show interfaces trunk
```

### Router Interfaces

``` cisco
show ip interface brief
```

### Routing Table

``` cisco
show ip route
```

### MAC Address Table

``` cisco
show mac address-table
```

### Connectivity Test

``` text
ping <destination-ip>
```

------------------------------------------------------------------------

## Testing & Results

Connectivity was tested between devices in different VLANs.

Example:

``` text
PC0 → PC7
192.168.10.x → 192.168.40.x
```

The successful ICMP replies confirm that R1 is routing traffic between
the VLANs.

The lab was also tested using Cisco Packet Tracer Simulation Mode to
observe ARP and ICMP packet flow.

------------------------------------------------------------------------

## Simulation Mode

The simulation demonstrates the packet path:

``` text
Source PC
   ↓
Access Switch
   ↓
802.1Q Trunk
   ↓
R1 Router
   ↓
802.1Q Trunk
   ↓
Destination Switch
   ↓
Destination PC
```

The PDU inspection was used to observe Layer 2 Ethernet information and
Layer 3 IP/ICMP information.

------------------------------------------------------------------------

## Screenshots

    \# Evidence
  ---- ------------------------------
    01 Final network topology
    02 SW1 VLAN configuration
    03 SW2 VLAN configuration
    04 SW1 trunk configuration
    05 SW2 trunk configuration
    06 Router subinterfaces
    07 Router routing table
    08 SW1 MAC address table
    09 Inter-VLAN routing ping
    10 Cross-VLAN connectivity test
    11 Simulation Mode: ARP & ICMP

Screenshots are available in the [`screenshots`](./screenshots)
directory.

------------------------------------------------------------------------

## Files

``` text
Inter-VLAN-Routing-Lab/
├── README.md
├── Inter-VLAN-Routing.pkt
└── screenshots/
```

The `.pkt` file contains the complete Cisco Packet Tracer topology and
configuration.

------------------------------------------------------------------------

## Learning Outcomes

By completing this lab, I practiced:

-   VLAN creation and segmentation
-   Access port configuration
-   802.1Q trunking
-   Router-on-a-Stick
-   Router subinterfaces
-   Inter-VLAN routing
-   ARP operation
-   MAC address learning
-   ICMP connectivity testing
-   Cisco IOS CLI
-   Network verification commands
-   Packet Tracer Simulation Mode
-   Saving and verifying device configurations

------------------------------------------------------------------------

## Status

**Completed**

**Platform:** Cisco Packet Tracer

**Focus:** CCNA Switching & Routing

**Lab Type:** Hands-on Networking Lab

------------------------------------------------------------------------

## Author

**Rahul Bagaria**

This lab is part of my hands-on CCNA networking practice and portfolio.
