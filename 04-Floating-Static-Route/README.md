# 🔀 Static Routing with Floating Static Route

![Cisco](https://img.shields.io/badge/Cisco-CCNA-1BA0D7)
![Packet Tracer](https://img.shields.io/badge/Cisco%20Packet%20Tracer-Lab-orange)
![Routing](https://img.shields.io/badge/Routing-Static%20%7C%20Floating%20Static-green)
![Status](https://img.shields.io/badge/Status-Completed-success)

A hands-on **Cisco networking lab** demonstrating static routing, route redundancy, Administrative Distance (AD), and automatic failover using a floating static route.

## 🎯 Objective

Build two paths between the `10.1.1.0/24` and `20.1.1.0/24` networks:

- **Primary:** `R1 → R2 → R4`
- **Backup:** `R1 → R3 → R4`
- Primary static route: **AD 1**
- Floating static route: **AD 10**
- Verify connectivity before and after a simulated R2-path failure.

## 🖧 Topology

```text
                    R2
             12.0.0.2 /24
              /          \
     12.0.0.0/24        24.0.0.0/24
            /              \
          R1                R4
         /  \              /  \
   10.1.1.1  13.0.0.1   20.1.1.1  SW2
       |        \          |
      SW1        R3       PCs
     /  \     13.0.0.2
   PCs  PCs      |
                 34.0.0.1
                   |
                  R4
```

### Path design

```text
PRIMARY
R1 ───── R2 ───── R4
AD 1

BACKUP / FLOATING
R1 ───── R3 ───── R4
AD 10
```

> Lower Administrative Distance is preferred. Therefore, the AD 1 route is installed while the AD 10 route remains available as the floating backup.

## 🌐 IP Addressing

| Device | Interface | IP Address | Purpose |
|---|---|---|---|
| R1 | G0/0 | 12.0.0.1/24 | R1–R2 |
| R1 | G0/1 | 13.0.0.1/24 | R1–R3 |
| R1 | G0/2 | 10.1.1.1/24 | LAN 1 |
| R2 | G0/0 | 12.0.0.2/24 | R1–R2 |
| R2 | G0/1 | 24.0.0.1/24 | R2–R4 |
| R3 | G0/1 | 13.0.0.2/24 | R1–R3 |
| R3 | G0/0 | 34.0.0.1/24 | R3–R4 |
| R4 | G0/0 | 34.0.0.2/24 | R3–R4 |
| R4 | G0/1 | 24.0.0.2/24 | R2–R4 |
| R4 | G0/2 | 20.1.1.1/24 | LAN 2 |

## ⚙️ Static Route Configuration

### R1 — Primary + Floating Backup

```cisco
ip route 20.1.1.0 255.255.255.0 12.0.0.2
ip route 20.1.1.0 255.255.255.0 13.0.0.2 10
```

### R2

```cisco
ip route 10.1.1.0 255.255.255.0 12.0.0.1
ip route 20.1.1.0 255.255.255.0 24.0.0.2
```

### R3

```cisco
ip route 10.1.1.0 255.255.255.0 13.0.0.1
ip route 20.1.1.0 255.255.255.0 34.0.0.2
```

### R4 — Primary + Floating Backup

```cisco
ip route 10.1.1.0 255.255.255.0 24.0.0.1
ip route 10.1.1.0 255.255.255.0 34.0.0.1 10
```

Full router configurations are available in the [`configuration`](./configuration/) folder.

## 🧪 Verification & Failover Test

### 1. Test the primary path

From the left-side laptop:

```text
ping 20.1.1.3
tracert 20.1.1.3
```

Expected path:

```text
Laptop → R1 → R2 → R4 → Destination
```

### 2. Simulate a failure

The R2 path was intentionally shut down to test redundancy.

Example:

```cisco
R1(config)# interface g0/0
R1(config-if)# shutdown
```

### 3. Test again

```text
ping 20.1.1.3
tracert 20.1.1.3
```

The traffic should now use:

```text
Laptop → R1 → R3 → R4 → Destination
```

This demonstrates the floating static route becoming the active route after the primary path becomes unavailable.

## 📸 Lab Evidence

- [`Primary path ping + traceroute`](./screenshots/01-primary-path-ping-traceroute.png)
- [`Backup path after R2 shutdown`](./screenshots/02-backup-path-after-r2-shutdown.png)
- [`Topology & failover infographic`](./topology-and-failover-infographic.png)

## 🔍 Useful Verification Commands

```cisco
show ip interface brief
show ip route
show ip route 20.1.1.0
show ip route 10.1.1.0
```

From a PC:

```text
ping <destination-ip>
tracert <destination-ip>
```

## 🧠 Key Learnings

- Configured static routes using next-hop addresses.
- Implemented two redundant paths between remote LANs.
- Used Administrative Distance to create a floating static route.
- Tested route failover by shutting down the primary path.
- Verified the new forwarding path using `ping` and `tracert`.
- Practiced basic network redundancy and troubleshooting.

## 🛠️ Tools

- Cisco Packet Tracer
- Cisco IOS CLI
- Static Routing
- Floating Static Routing
- Ping / Traceroute

## 📁 Repository Structure

```text
CCNA-Labs/
└── 04-Floating-Static-Route/
    ├── README.md
    ├── topology-and-failover-infographic.png
    ├── configuration/
    │   ├── R1.txt
    │   ├── R2.txt
    │   ├── R3.txt
    │   └── R4.txt
    └── screenshots/
        ├── 01-primary-path-ping-traceroute.png
        └── 02-backup-path-after-r2-shutdown.png
```

---

### 📌 Skills Demonstrated

`CCNA` `Cisco IOS` `Static Routing` `Floating Static Route` `Administrative Distance` `Route Redundancy` `Failover` `Troubleshooting` `Packet Tracer`
