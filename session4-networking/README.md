# Session 4: Linux Networking Commands Homework

---

## 👤 Student Information
* **Name:** Nachiketas Iyer
* **Enrollment Number:** 24bcs10131
* **Repository:** [devops-heros](https://github.com/Nachiket115/devops-heros)
* **Branch:** `session4-networking`

---

## 📖 Overview
This document covers the execution, output analysis, and fundamental concepts of core Linux/Unix networking diagnostic and communication commands: **`ip a`**, **`ping`**, **`traceroute`**, **`netstat`**, and **`curl`**.

---

## 🛠️ Networking Commands Breakdown

---

### 1. `ip a` (IP Address & Interface Information)

#### 📝 Explanation & What I Understood:
* **Purpose:** The `ip a` (short for `ip address show`) command is part of the `iproute2` utility suite in modern Linux. It displays all active and inactive network interfaces, their MAC addresses (link layer), assigned IPv4 (`inet`) and IPv6 (`inet6`) addresses, subnet masks (in CIDR notation, e.g., `/16` or `/24`), MTU (Maximum Transmission Unit), and interface operational states (`UP` or `DOWN`).
* **Key Insights:**
  * `lo` (Loopback): Used for internal inter-process communication on `127.0.0.1/8` without touching physical network hardware.
  * `eth0` / `en0`: Primary network interface carrying outbound traffic to local gateways and the internet.
  * `mtu`: Specifies the largest packet size (in bytes) that the interface can transmit without fragmentation.

#### 💻 Command:
```bash
ip a
```

#### 📊 Execution Output:
```bash
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
11: eth0@if17: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 65535 qdisc noqueue state UP 
    link/ether 0e:7c:4c:54:b1:bb brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

---

### 2. `ping` (Packet Internet Groper)

#### 📝 Explanation & What I Understood:
* **Purpose:** `ping` tests network layer (Layer 3) connectivity and reachability between the local system and a remote host (using domain name or IP) via the **ICMP (Internet Control Message Protocol)** Echo Request and Echo Reply messages.
* **Key Insights:**
  * **Packet Loss:** 0.0% packet loss indicates a completely stable and uninterrupted network connection.
  * **RTT (Round-Trip Time):** Shows latency in milliseconds (min/avg/max/stddev). A lower latency means faster communication.
  * **TTL (Time to Live):** Number of router hops remaining before a packet expires, preventing infinite routing loops.

#### 💻 Command:
```bash
ping -c 4 8.8.8.8
```

#### 📊 Execution Output:
```bash
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: icmp_seq=0 ttl=114 time=50.782 ms
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=30.044 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=16.729 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=20.916 ms

--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 16.729/29.618/50.782/13.133 ms
```

---

### 3. `traceroute` (Trace Network Route)

#### 📝 Explanation & What I Understood:
* **Purpose:** `traceroute` maps out the step-by-step route (network hops) taken by packets traveling from the source machine to the destination host across the internet or local intranet.
* **Key Insights:**
  * It incrementally increases the IP packet's **TTL** field starting from 1. Each intermediate router decrements the TTL; when TTL reaches 0, the router discards the packet and sends back an `ICMP Time Exceeded` message, revealing that hop's IP address.
  * **Asterisks (`* * *`):** Indicate routers or firewalls configured to drop ICMP/UDP probe responses for security.
  * Helps identify network bottlenecks, high latency points, or routing failures along the network path.

#### 💻 Command:
```bash
traceroute -m 5 -w 2 8.8.8.8
```

#### 📊 Execution Output:
```bash
traceroute to 8.8.8.8 (8.8.8.8), 5 hops max, 40 byte packets
 1  dns.nfen (192.168.1.1)  22.928 ms  179.600 ms  40.188 ms
 2  static-193.79.194.14-tataidc.co.in (14.194.79.193)  46.077 ms * *
 3  10.117.202.153 (10.117.202.153)  42.313 ms  48.297 ms  46.695 ms
 4  10.129.34.214 (10.129.34.214)  37.703 ms  75.375 ms  92.457 ms
 5  * * *
```

---

### 4. `netstat` (Network Statistics)

#### 📝 Explanation & What I Understood:
* **Purpose:** `netstat` (Network Statistics) inspects network connections, active routing tables, interface statistics, and listening TCP/UDP ports.
* **Key Insights:**
  * **`LISTEN` State:** Denotes server processes waiting for incoming connection requests on a designated port.
  * **Foreign Address (`*.*`):** Represents that any remote IP can attempt a connection to that listening service.
  * Essential for identifying port conflicts, verifying whether a newly deployed web or database service is actively listening on its expected port, and detecting unauthorized open ports.

#### 💻 Command:
```bash
netstat -an | grep LISTEN | head -n 10
```

#### 📊 Execution Output:
```bash
tcp4       0      0  *.57140                *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56857        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56848        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56847        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56846        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56830        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56829        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56817        *.*                    LISTEN     
tcp4       0      0  127.0.0.1.56816        *.*                    LISTEN     
tcp6       0      0  *.56474                *.*                    LISTEN     
```

---

### 5. `curl` (Client URL)

#### 📝 Explanation & What I Understood:
* **Purpose:** `curl` is a command-line tool for transferring data to or from a network server using protocols such as HTTP, HTTPS, FTP, and WebSockets.
* **Key Insights:**
  * **`curl -I` / `curl -i`:** Fetches HTTP response headers (Status code `200 OK`, `Content-Type`, `Server`, `Date`, cache headers).
  * **API Testing & Automation:** Used extensively in DevOps CI/CD pipelines and microservice health checks to verify that endpoints return the expected status codes and payloads.
  * Supports custom headers (`-H`), request methods (`-X POST`, `-X PUT`), and authentication tokens (`-u` or `-H "Authorization: Bearer ..."`).

#### 💻 Command:
```bash
curl -I https://httpbin.org/get
```

#### 📊 Execution Output:
```bash
HTTP/2 200 
date: Thu, 03 Sep 2026 09:03:30 GMT
content-type: application/json
content-length: 254
server: gunicorn/19.9.0
access-control-allow-origin: *
access-control-allow-credentials: true
```

---

## 📚 Summary Table of Commands

| Command | Layer (OSI) | Primary Use Case | Example Diagnostic Workflow |
| :--- | :--- | :--- | :--- |
| **`ip a`** | Data Link / Network (L2/L3) | Check local IP, MAC address, interface state | Is my interface `UP` and does it have a valid IP? |
| **`ping`** | Network (L3 - ICMP) | Check reachability and measure round-trip latency | Is the server reachable and responding? |
| **`traceroute`** | Network (L3) | Trace path and identify slow or dropped router hops | Where along the path is my connection failing or slowing down? |
| **`netstat`** | Transport (L4 - TCP/UDP) | Inspect active sockets, listening ports, connections | Is my web server listening on port 80/443? |
| **`curl`** | Application (L7 - HTTP/S) | Fetch URL content, inspect API responses & headers | Is my REST API returning HTTP `200 OK` with JSON payload? |

---

## 🔗 Practice Resources & References

From [**`session4-networking/resources.md`**](file:///Users/nachiketr/devops-heros/session4-networking/resources.md) and [**`session4-networking/ip.md`**](file:///Users/nachiketr/devops-heros/session4-networking/ip.md):
* [Network Troubleshooting Guide](https://github.com/Nency-Ravaliya/Network-Troubleshooting)
* [OSI & Network Devices](https://github.com/Nency-Ravaliya/OSI-Network-devices)
* [Subnetting Guide & Practice](https://github.com/Nency-Ravaliya/Subnetting)
* [IP-Quest (IP Addressing & CIDR)](https://github.com/Nency-Ravaliya/IP-quest)
* [How DHCP Works](https://github.com/Nency-Ravaliya/How-DHCP-Works)
