# Address Resolution Protocol (ARP) Lab

## Overview

This lab demonstrates how Address Resolution Protocol (ARP) resolves IPv4 addresses to MAC addresses in a local network and how routers perform ARP independently for different broadcast domains.

---

## Objectives

- Understand ARP Request
- Understand ARP Reply
- Observe ARP Cache
- Verify ARP using Packet Tracer
- Understand why ARP does not cross routers

---

## Network Topology

![Topology](screenshots/topology.png)

---

## IP Addressing

| Device | IP Address | Subnet Mask | Default Gateway |
|---------|------------|-------------|-----------------|
| Laptop0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| Laptop1 | 192.168.1.20 | 255.255.255.0 | 192.168.1.1 |
| Router G0/0 | 192.168.1.1 | 255.255.255.0 | - |
| Router G0/1 | 192.168.2.1 | 255.255.255.0 | - |
| Laptop2 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |
| Laptop3 | 192.168.2.20 | 255.255.255.0 | 192.168.2.1 |

---

## Router Configuration

```bash
enable

configure terminal

interface g0/0/0
ip address 192.168.1.1 255.255.255.0
no shutdown

interface g0/0/1
ip address 192.168.2.1 255.255.255.0
no shutdown

end
write memory
```

---

## Commands Used

```bash
arp -a
arp -d
ping
show ip arp
show ip interface brief
```

---

## Verification

### Empty ARP Cache

![Empty ARP Cache](screenshots/empty-arp-cache.png)

---

### ARP Request

![ARP Request](screenshots/arp-request.png)

---

### ARP Reply

![ARP Reply](screenshots/arp-reply.png)

---

### Successful Ping

![Ping](screenshots/successful-ping.png)

---

### Router ARP Table

![Router ARP](screenshots/router-arp-table.png)

---

## Key Learnings

- ARP maps IPv4 addresses to MAC addresses.
- ARP Requests are Layer 2 broadcasts.
- ARP Replies are unicast.
- ARP works only inside the local broadcast domain.
- Routers do not forward ARP broadcasts.
- A host communicates with remote networks using the MAC address of its default gateway.

---

## Interview Questions

### What is ARP?

Address Resolution Protocol maps an IPv4 address to a MAC address on a local network.

### Why doesn't ARP cross routers?

Because ARP uses Layer 2 broadcast frames, and routers do not forward Layer 2 broadcasts.

### Which command displays the ARP table?

```bash
show ip arp
```

---

## Files

- ARP-Lab.pkt
- README.md
- Screenshots

---

## Author

Rahul Bagaria
