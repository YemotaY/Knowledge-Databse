# TCP/IP Protocol Stack

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

The TCP/IP protocol stack is the foundation of internet communication, providing a layered approach to network communication that enables reliable data transmission across diverse networks worldwide.

## Overview

TCP/IP (Transmission Control Protocol/Internet Protocol) is a suite of protocols that defines how data should be packetized, addressed, transmitted, routed, and received across networks. It forms the backbone of the internet and most modern networks.

## TCP/IP Layer Model

### 4-Layer TCP/IP Model
```
┌─────────────────────────────────────┐
│        Application Layer            │  ← HTTP, FTP, SMTP, DNS
├─────────────────────────────────────┤
│        Transport Layer              │  ← TCP, UDP
├─────────────────────────────────────┤
│        Internet Layer               │  ← IP, ICMP, ARP
├─────────────────────────────────────┤
│        Network Access Layer         │  ← Ethernet, Wi-Fi
└─────────────────────────────────────┘
```

### Comparison with OSI Model
```
OSI Model          TCP/IP Model       Examples
─────────────────────────────────────────────────
Application   ┐    Application      HTTP, FTP, SMTP
Presentation  │         ↑           TLS, compression
Session       ┘         ↑           NetBIOS, RPC
─────────────────────────────────────────────────
Transport          Transport        TCP, UDP
─────────────────────────────────────────────────
Network            Internet         IP, ICMP, ARP
─────────────────────────────────────────────────
Data Link     ┐    Network Access   Ethernet frames
Physical      ┘         ↓           Cables, Wi-Fi
```

## Internet Protocol (IP)

### IPv4 Header Structure
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│Version│ IHL │    ToS    │           Total Length           │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│         Identification        │Flags│    Fragment Offset   │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│   TTL   │   Protocol  │          Header Checksum          │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                     Source Address                        │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                  Destination Address                      │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                   Options (if any)                        │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
```

**Key Fields:**
- **Version**: IP version (4 for IPv4, 6 for IPv6)
- **TTL**: Time to Live (hop limit)
- **Protocol**: Next layer protocol (6=TCP, 17=UDP)
- **Source/Destination**: IP addresses

### IPv4 Address Structure
```
Class A: 0xxxxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx
         Network   Host      Host      Host
         1.0.0.0 - 126.255.255.255 (/8)

Class B: 10xxxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx  
         Network   Network   Host      Host
         128.0.0.0 - 191.255.255.255 (/16)

Class C: 110xxxxx.xxxxxxxx.xxxxxxxx.xxxxxxxx
         Network   Network   Network   Host
         192.0.0.0 - 223.255.255.255 (/24)

Private Ranges:
- 10.0.0.0/8        (Class A private)
- 172.16.0.0/12     (Class B private)  
- 192.168.0.0/16    (Class C private)
```

### IPv6 Improvements
```
IPv4 vs IPv6 Comparison:

Feature          IPv4              IPv6
─────────────────────────────────────────────
Address Space    32 bits           128 bits
                 ~4.3 billion      ~3.4×10³⁸ addresses
Header Size      20-60 bytes       40 bytes (fixed)
Fragmentation    Routers & hosts   Source only
Security         Optional IPSec    Built-in IPSec
Auto-config      DHCP required     SLAAC available
QoS              Best effort       Flow labels

IPv6 Address Format:
2001:0db8:85a3:0000:0000:8a2e:0370:7334
├─┤ ├──┤ ├──┤ ├──┤ ├──┤ ├──┤ ├──┤ ├──┤
Global   Subnet      Interface Identifier
Prefix   ID          (64 bits)
```

## Transmission Control Protocol (TCP)

### TCP Header Structure
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│          Source Port          │       Destination Port       │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                        Sequence Number                       │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                    Acknowledgment Number                     │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│ Offset│ Res │U│A│P│R│S│F│          Window Size             │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│           Checksum            │         Urgent Pointer        │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                    Options (if any)                          │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
```

### TCP Three-Way Handshake
```
Client                Server
  │                     │
  │──── SYN(seq=x) ────→│  1. Client initiates connection
  │                     │
  │←─ SYN-ACK(seq=y,────│  2. Server responds with SYN-ACK
  │    ack=x+1)         │
  │                     │
  │─── ACK(seq=x+1, ───→│  3. Client acknowledges
  │    ack=y+1)         │
  │                     │
  │ ═══ ESTABLISHED ═══ │  Connection established
  │                     │

State Transitions:
Client: CLOSED → SYN-SENT → ESTABLISHED
Server: CLOSED → LISTEN → SYN-RCVD → ESTABLISHED
```

### TCP Connection Termination
```
Client                Server
  │                     │
  │──── FIN(seq=u) ────→│  1. Client initiates close
  │                     │
  │←─── ACK(ack=u+1) ───│  2. Server acknowledges
  │                     │
  │←─── FIN(seq=v) ────│  3. Server sends FIN
  │                     │
  │──── ACK(ack=v+1) ──→│  4. Client acknowledges
  │                     │
  │    CONNECTION       │
  │      CLOSED         │

Four-way handshake (graceful close)
Both sides can initiate termination
```

### TCP Flow Control & Congestion Control

**Sliding Window Protocol:**
```
Sender Window:
┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│1│2│3│4│5│6│7│8│9│10│11│12│
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
  ↑     ↑           ↑
Sent & Next to     Window
ACKed  send        size

Receiver Window:
┌─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┬─┐
│ │ │ │ │5│6│7│8│ │ │ │ │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
        ↑       ↑
    Next exp.  Window
    sequence   size
```

**Congestion Control Algorithms:**
```
1. Slow Start:
   cwnd = 1 MSS
   For each ACK: cwnd += 1 MSS
   
2. Congestion Avoidance:
   For each ACK: cwnd += MSS/cwnd
   
3. Fast Retransmit:
   3 duplicate ACKs → retransmit
   
4. Fast Recovery:
   cwnd = ssthresh + 3 MSS

Congestion Window Growth:
        cwnd ↑
             │    ╱╲ 
       28    │   ╱  ╲
       24    │  ╱    ╲    ← Congestion avoidance
       20    │ ╱      ╲
       16    │╱        ╲
       12    ├          ╲
        8    ├           ╲
        4    ├            ╲
        1    ├─────────────╲───→ time
             │ Slow   Fast  │
             │ start  recov │
             │        ery   │
             └──────────────┘
```

## User Datagram Protocol (UDP)

### UDP Header Structure
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│          Source Port          │       Destination Port       │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│            Length             │           Checksum            │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                             Data                              │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘
```

### TCP vs UDP Comparison
```
Feature              TCP                 UDP
─────────────────────────────────────────────────────
Connection           Connection-oriented Connectionless
Reliability          Reliable            Best effort
Ordering             Ordered delivery    No ordering guarantee
Flow Control         Yes                 No
Congestion Control   Yes                 No
Header Size          20-60 bytes         8 bytes
Error Checking       Checksum + retrans  Checksum only
Speed                Slower (overhead)   Faster (minimal overhead)

Use Cases:
TCP: Web browsing, file transfer, email
UDP: DNS queries, video streaming, gaming, IoT
```

## Routing and Addressing

### Subnetting Example
```
Network: 192.168.1.0/24

Subnet into 4 networks:
192.168.1.0/26   → 192.168.1.0   - 192.168.1.63   (64 hosts)
192.168.1.64/26  → 192.168.1.64  - 192.168.1.127  (64 hosts)
192.168.1.128/26 → 192.168.1.128 - 192.168.1.191  (64 hosts)
192.168.1.192/26 → 192.168.1.192 - 192.168.1.255  (64 hosts)

Subnet Mask Calculation:
/26 = 255.255.255.192
Binary: 11111111.11111111.11111111.11000000
                                    └─────┘
                                   6 host bits = 2⁶ = 64 addresses
```

### Routing Table Example
```
Destination     Gateway         Interface   Metric
0.0.0.0/0       192.168.1.1    eth0        1      ← Default route
192.168.1.0/24  0.0.0.0        eth0        0      ← Local network
10.0.0.0/8      192.168.1.100  eth0        10     ← Remote network
127.0.0.0/8     127.0.0.1      lo          0      ← Loopback

Longest Prefix Match:
Packet to 192.168.1.50:
1. Check 192.168.1.0/24  ✓ (most specific)
2. Check 0.0.0.0/0       ✓ (less specific)
→ Route via local interface (eth0)
```

## Network Address Translation (NAT)

### NAT Operation
```
Private Network               Public Network
192.168.1.0/24               203.0.113.1

Internal Host    NAT Router    External Server
192.168.1.100   ┌─────────┐   203.0.113.50
      │         │         │         │
      │─────────│ NAT     │─────────│
      │ src:    │ Table   │ src:    │
      │192.168.1│         │203.0.113│
      │.100:1234│         │.1:5000  │
      │         └─────────┘         │

NAT Translation Table:
Internal IP:Port    External IP:Port    External Server
192.168.1.100:1234  203.0.113.1:5000   203.0.113.50:80
192.168.1.101:2345  203.0.113.1:5001   8.8.8.8:53
192.168.1.102:3456  203.0.113.1:5002   10.0.0.1:443
```

## Quality of Service (QoS)

### Traffic Classification
```
Traffic Type     Priority    Bandwidth    Latency     Jitter
──────────────────────────────────────────────────────────
Voice/VoIP       Highest     64-128 Kbps  <150ms     <30ms
Video Conf       High        384K-2Mbps   <400ms     <50ms  
Streaming Video  Medium      1-8 Mbps     <5s        <1s
Web Browsing     Medium      Varies       <2s        N/A
File Transfer    Low         Best effort  No limit   N/A
Backup           Lowest      Background   No limit   N/A

QoS Mechanisms:
1. Traffic Shaping: Smooth traffic bursts
2. Priority Queuing: Process high-priority first  
3. Bandwidth Allocation: Reserve capacity
4. Packet Marking: DSCP/ToS field marking
```

## Security Considerations

### Common Network Attacks
```
Attack Type          Description                    Mitigation
─────────────────────────────────────────────────────────────
SYN Flood           Exhaust server resources       SYN cookies, rate limiting
IP Spoofing         Fake source IP addresses       Ingress filtering, IPSec
Man-in-the-Middle   Intercept communications       TLS/SSL, certificate validation
DDoS                Overwhelm with traffic          Rate limiting, load balancing
Port Scanning       Discover open services          Firewalls, intrusion detection
Packet Sniffing     Capture network traffic         Encryption (TLS/VPN)

Defense in Depth:
┌─────────────────────────────────────────┐
│               Application               │ ← Input validation, auth
├─────────────────────────────────────────┤
│               Transport                 │ ← TLS/SSL encryption
├─────────────────────────────────────────┤  
│               Network                   │ ← Firewalls, IPS/IDS
├─────────────────────────────────────────┤
│             Data Link                   │ ← VLANs, MAC filtering
├─────────────────────────────────────────┤
│              Physical                   │ ← Physical security
└─────────────────────────────────────────┘
```

## Performance Optimization

### Network Performance Metrics
```python
# Example network performance measurement

import time
import socket

def measure_latency(host, port):
    """Measure round-trip time to a host"""
    start = time.time()
    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(5)
        result = sock.connect_ex((host, port))
        sock.close()
        end = time.time()
        if result == 0:
            return (end - start) * 1000  # Convert to milliseconds
    except:
        return None
    return None

def measure_throughput(data_size_mb=10):
    """Measure network throughput"""
    # Simulate data transfer
    start = time.time()
    # ... actual data transfer code ...
    end = time.time()
    
    transfer_time = end - start
    throughput_mbps = (data_size_mb * 8) / transfer_time
    return throughput_mbps

# Performance optimization techniques:
# 1. TCP window scaling
# 2. Nagle's algorithm tuning  
# 3. Receive buffer optimization
# 4. Connection pooling
# 5. Keep-alive connections
```

The TCP/IP stack forms the foundation of modern internet communication, enabling reliable, scalable, and efficient data transmission across diverse networks. Understanding these protocols is essential for network programming, system administration, and troubleshooting network issues.
