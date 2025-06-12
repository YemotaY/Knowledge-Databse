# DNS Protocol (Domain Name System)

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

DNS (Domain Name System) is a hierarchical distributed naming system that translates human-readable domain names into IP addresses, serving as the internet's phonebook and enabling users to access websites using memorable names instead of numeric addresses.

## Overview

DNS operates as a distributed database that maps domain names to IP addresses and other resource records. It uses a hierarchical structure with multiple levels of servers to efficiently resolve domain names across the global internet.

## DNS Hierarchy

### DNS Tree Structure
```
                    Root (.)
                      │
        ┌─────────────┼─────────────┐
        │             │             │
      .com          .org          .net
        │             │             │
    ┌───┼───┐     ┌───┼───┐     ┌───┼───┐
    │   │   │     │   │   │     │   │   │
 google example wikipedia github cloudflare cdn77
    │       │         │       │        │
  ┌─┼─┐   ┌─┼─┐     ┌─┼─┐   ┌─┼─┐    ┌─┼─┐
  │ │ │   │ │ │     │ │ │   │ │ │    │ │ │
 www mail api www docs  en  www api  www cdn

Full Domain Names (FQDN):
- www.google.com.
- mail.google.com.
- en.wikipedia.org.
- api.github.com.
```

### DNS Server Types
```
Server Type          Function                           Example
─────────────────────────────────────────────────────────────────────
Root Servers         Top-level authority               a.root-servers.net
TLD Servers          Top-level domain authority        a.gtld-servers.net
Authoritative        Domain-specific authority         ns1.google.com
Recursive Resolver   Client query processor            8.8.8.8 (Google)
Caching Resolver     Local cache + recursion           Router DNS cache
Forwarder           Forwards to other resolvers        Corporate DNS

Hierarchy Flow:
Client → Recursive Resolver → Root → TLD → Authoritative → Response
```

## DNS Query Process

### Recursive DNS Resolution
```
1. Client Query:
   User types: www.example.com
   
2. Recursive Resolver Check:
   ┌─ Cache hit? → Return cached result
   └─ Cache miss → Continue resolution

3. Root Server Query:
   Resolver → Root Server: "Where is .com?"
   Root → Resolver: "Ask TLD server a.gtld-servers.net"

4. TLD Server Query:
   Resolver → TLD Server: "Where is example.com?"
   TLD → Resolver: "Ask ns1.example.com"

5. Authoritative Server Query:
   Resolver → Auth Server: "What is www.example.com?"
   Auth → Resolver: "IP address is 93.184.216.34"

6. Response to Client:
   Resolver → Client: "www.example.com = 93.184.216.34"

Timeline:
Client    Resolver    Root    TLD    Authoritative
  │         │          │      │           │
  │── Q ───→│          │      │           │   Query: www.example.com
  │         │── Q ────→│      │           │   Where is .com?
  │         │←── R ────│      │           │   Ask a.gtld-servers.net
  │         │               │           │
  │         │── Q ──────────→│           │   Where is example.com?
  │         │←── R ──────────│           │   Ask ns1.example.com
  │         │                         │
  │         │── Q ─────────────────────→│   What is www.example.com?
  │         │←── R ─────────────────────│   IP: 93.184.216.34
  │         │                         │
  │←── R ───│          │      │           │   www.example.com = 93.184.216.34
```

### Iterative vs Recursive Queries
```
Recursive Query (client → resolver):
- Client asks resolver to find complete answer
- Resolver does all the work
- Client gets final answer

Iterative Query (resolver → servers):
- Resolver asks each server in hierarchy
- Each server provides next step or final answer
- Resolver follows referrals until complete

Query Types:
┌─────────────────┐     ┌──────────────────┐
│     Client      │────→│   Recursive      │ Recursive
│   (Browser)     │←────│   Resolver       │ Response
└─────────────────┘     └──────────────────┘
                                │ ↑
                         Iterative │ │ Iterative
                          Queries  │ │ Responses
                                ↓ │
                        ┌──────────────────┐
                        │   DNS Servers    │
                        │  (Root/TLD/Auth) │
                        └──────────────────┘
```

## DNS Message Format

### DNS Message Structure
```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                      Transaction ID                           │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│QR│  Opcode │AA│TC│RD│RA│ Z │   RCODE   │      
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                      Question Count                          │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                      Answer Count                            │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                   Authority Record Count                     │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                   Additional Record Count                    │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                                                              │
│                      Question Section                        │
│                                                              │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                                                              │
│                       Answer Section                         │
│                                                              │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                                                              │
│                     Authority Section                        │
│                                                              │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                                                              │
│                    Additional Section                        │
│                                                              │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘

Header Flags:
QR:  Query/Response (0=query, 1=response)  
AA:  Authoritative Answer
TC:  Truncated (message too long for UDP)
RD:  Recursion Desired
RA:  Recursion Available
Z:   Reserved (must be 0)
RCODE: Response Code (0=success, 3=name error)
```

### DNS Record Format
```
Name:     Domain name (compressed)
Type:     Record type (A, AAAA, CNAME, etc.)
Class:    Usually IN (Internet)
TTL:      Time to live (seconds)
RDLength: Resource data length
RData:    Resource data (IP address, hostname, etc.)

Example Resource Record:
www.example.com.  IN  A     300   93.184.216.34
│                 │   │     │     │
Name              │   Type  TTL   Data
                  Class
```

## DNS Record Types

### Common DNS Record Types
```
Type    Purpose                   Format                           Example
────────────────────────────────────────────────────────────────────────────
A       IPv4 address             32-bit IP address               192.0.2.1
AAAA    IPv6 address             128-bit IP address              2001:db8::1
CNAME   Canonical name           Domain name                      www.example.com
MX      Mail exchange            Priority + domain name          10 mail.example.com
NS      Name server              Domain name                      ns1.example.com
PTR     Reverse lookup           Domain name                      1.2.0.192.in-addr.arpa
TXT     Text information         Arbitrary text                   "v=spf1 include:_spf.google.com ~all"
SOA     Start of authority       Multiple fields                  See below
SRV     Service location         Priority Weight Port Target     10 5 443 api.example.com
CAA     Certificate authority    Flags Tag Value                 0 issue "letsencrypt.org"
DNSKEY  DNSSEC public key        Flags Protocol Algorithm Key    See DNSSEC section
DS      Delegation signer        Key tag Algorithm Digest type   See DNSSEC section
```

### DNS Record Examples
```bash
# A Record (IPv4)
www.example.com.    300    IN    A    93.184.216.34

# AAAA Record (IPv6)  
www.example.com.    300    IN    AAAA 2606:2800:220:1:248:1893:25c8:1946

# CNAME Record (Alias)
www.example.com.    300    IN    CNAME example.com.
blog.example.com.   300    IN    CNAME blogger.example.com.

# MX Record (Email)
example.com.        300    IN    MX    10 mail1.example.com.
example.com.        300    IN    MX    20 mail2.example.com.

# TXT Record (Various uses)
example.com.        300    IN    TXT   "v=spf1 include:_spf.google.com ~all"
_dmarc.example.com. 300    IN    TXT   "v=DMARC1; p=reject; rua=mailto:dmarc@example.com"

# SRV Record (Service Discovery)
_sip._tcp.example.com. 300 IN    SRV   10 5 5060 sip1.example.com.
_http._tcp.example.com. 300 IN   SRV   10 5 80 www.example.com.

# NS Record (Name Servers)
example.com.        86400  IN    NS    ns1.example.com.
example.com.        86400  IN    NS    ns2.example.com.

# SOA Record (Start of Authority)
example.com.        86400  IN    SOA   ns1.example.com. admin.example.com. (
                                      2025061201  ; Serial number
                                      3600        ; Refresh interval
                                      1800        ; Retry interval  
                                      604800      ; Expire time
                                      86400       ; Minimum TTL
                                      )
```

## DNS Caching

### Caching Hierarchy
```
DNS Cache Levels:

1. Browser Cache
   └─ TTL: 60-300 seconds
   └─ Chrome: chrome://net-internals/#dns

2. Operating System Cache
   └─ Windows: ipconfig /displaydns
   └─ Linux: systemd-resolve --statistics
   └─ macOS: dscacheutil -q host -a name example.com

3. Router/Local DNS
   └─ Home router cache
   └─ Corporate DNS servers

4. ISP Recursive Resolver
   └─ Large cache serving many users
   └─ Shared cache benefits

5. Public DNS Resolvers
   └─ Google (8.8.8.8), Cloudflare (1.1.1.1)
   └─ Global anycast infrastructure

Cache Behavior:
┌─────────────────┐  Cache Hit  ┌─────────────────┐
│     Client      │────────────→│   Local Cache   │
│                 │←────────────│   (Fast)        │
└─────────────────┘  <1ms       └─────────────────┘
         │                               │
         │ Cache Miss                    │ Cache Miss
         ↓                               ↓
┌─────────────────┐              ┌─────────────────┐
│ Recursive Query │              │   TTL Expired   │  
│   (Slow)        │              │   Revalidate    │
│   100-500ms     │              │                 │
└─────────────────┘              └─────────────────┘
```

### TTL (Time To Live) Strategy
```
Record Type     Typical TTL    Reason
──────────────────────────────────────────────────────────
A/AAAA          300-3600s      Balance performance/flexibility
CNAME           300-3600s      Alias resolution
MX              3600-86400s    Email routing stability  
NS              86400s         Name server stability
TXT             300-3600s      Verification/config changes
SOA             86400s         Zone authority information

TTL Considerations:
- Lower TTL: Faster changes, more DNS traffic
- Higher TTL: Better performance, slower changes
- Common values: 300s (5min), 3600s (1hr), 86400s (24hr)

Cache Poisoning Protection:
- Query ID randomization
- Source port randomization  
- DNSSEC validation
- Response validation
```

## DNS Security (DNSSEC)

### DNSSEC Overview
```
DNSSEC Chain of Trust:

Root Zone (.)
    │ Signed with Root KSK
    └─ DS record for .com

.com Zone
    │ Signed with .com ZSK  
    └─ DS record for example.com

example.com Zone
    │ Signed with example.com ZSK
    └─ RRSIG for www.example.com

Validation Process:
1. Verify root zone signature
2. Verify .com zone delegation
3. Verify example.com zone
4. Verify target record signature
```

### DNSSEC Record Types
```
Record Type    Purpose                           Example
─────────────────────────────────────────────────────────────────
DNSKEY         Public key for zone signing      Zone Signing Key (ZSK)
                                                Key Signing Key (KSK)

DS             Delegation Signer                Hash of child zone's KSK
               Links parent to child zone       Stored in parent zone

RRSIG          Digital signature                Signature over RRset
               Proves record authenticity       Time validity window

NSEC           Next Secure                      Proves non-existence
               Authenticated denial             Lists next domain name

NSEC3          Next Secure v3                   Hashed names for privacy
               Prevents zone walking            Salt + iterations

NSEC3PARAM     NSEC3 parameters                Hash algorithm parameters
               Zone-wide NSEC3 settings        Salt, iterations, flags

Example DNSSEC Records:
example.com. IN DNSKEY 257 3 8 AwEAAcHp...  (KSK)
example.com. IN DNSKEY 256 3 8 AwEAAb5q...  (ZSK)
example.com. IN DS 12345 8 2 A1B2C3D4...    (in .com zone)
www.example.com. IN RRSIG A 8 3 300 20250701000000 20250601000000 54321 example.com. ABC123...
```

## Modern DNS Features

### DNS over HTTPS (DoH) and DNS over TLS (DoT)
```
Traditional DNS (Plain text):
Client ─────UDP/53────→ DNS Server
       Unencrypted     
       ISP can see all queries

DNS over TLS (DoT):
Client ─────TLS/853───→ DNS Server  
       Encrypted
       Port 853

DNS over HTTPS (DoH):
Client ─────HTTPS/443─→ DNS Server
       Encrypted  
       Looks like web traffic
       Hard to block

DoH Example:
GET /dns-query?dns=AAABAAABAAAAAAAAA3d3dwdleGFtcGxlA2NvbQAAAQAB HTTP/2
Host: cloudflare-dns.com
Accept: application/dns-message

Popular DoH/DoT Providers:
- Cloudflare: 1.1.1.1 (DoT), https://cloudflare-dns.com/dns-query (DoH)
- Google: 8.8.8.8 (DoT), https://dns.google/dns-query (DoH)  
- Quad9: 9.9.9.9 (DoT), https://dns.quad9.net/dns-query (DoH)
```

### DNS Load Balancing
```
Geographic Load Balancing:
www.example.com (US clients)    → 192.0.2.1 (US server)
www.example.com (EU clients)    → 203.0.113.1 (EU server)  
www.example.com (Asia clients)  → 198.51.100.1 (Asia server)

Round Robin Load Balancing:
www.example.com. 300 IN A 192.0.2.1
www.example.com. 300 IN A 192.0.2.2  
www.example.com. 300 IN A 192.0.2.3

Weighted Load Balancing:
Server A: 70% traffic (higher capacity)
Server B: 20% traffic (medium capacity)
Server C: 10% traffic (backup server)

Health Check Integration:
- Remove failed servers from DNS
- Automatic failover
- Traffic shifting based on performance
```

## DNS Performance Optimization

### DNS Performance Metrics
```python
import time
import socket
import dns.resolver

def measure_dns_performance(domain, record_type='A'):
    """Measure DNS resolution performance"""
    resolver = dns.resolver.Resolver()
    
    # Test different DNS servers
    dns_servers = [
        '8.8.8.8',      # Google
        '1.1.1.1',      # Cloudflare  
        '9.9.9.9',      # Quad9
        '208.67.222.222' # OpenDNS
    ]
    
    results = {}
    
    for server in dns_servers:
        resolver.nameservers = [server]
        times = []
        
        for _ in range(5):  # 5 measurements
            start = time.time()
            try:
                answer = resolver.resolve(domain, record_type)
                end = time.time()
                times.append((end - start) * 1000)  # Convert to ms
            except Exception as e:
                times.append(None)
        
        # Calculate statistics
        valid_times = [t for t in times if t is not None]
        if valid_times:
            results[server] = {
                'avg': sum(valid_times) / len(valid_times),
                'min': min(valid_times),
                'max': max(valid_times),
                'success_rate': len(valid_times) / len(times)
            }
    
    return results

# Example usage
perf_results = measure_dns_performance('www.google.com')
for server, stats in perf_results.items():
    print(f"{server}: Avg {stats['avg']:.1f}ms, "
          f"Min {stats['min']:.1f}ms, "
          f"Success {stats['success_rate']*100:.0f}%")
```

### DNS Optimization Strategies
```
Client-Side Optimization:
1. DNS Prefetching
   <link rel="dns-prefetch" href="//cdn.example.com">
   
2. Connection Keep-Alive  
   Reuse connections for multiple queries
   
3. Local Caching
   Browser, OS, and application-level caches
   
4. Multiple A Records
   Client can choose fastest responding server

Server-Side Optimization:
1. Anycast DNS
   Route to nearest server geographically
   
2. Low TTL for Dynamic Content
   Quick updates for changing records
   
3. High TTL for Static Content  
   Reduce DNS load for stable records
   
4. EDNS0 Support
   Larger UDP packets, reduced truncation

Infrastructure Optimization:
1. CDN Integration
   DNS directs to nearest edge server
   
2. Health Monitoring
   Remove unhealthy servers from DNS
   
3. Traffic Shaping
   Distribute load based on capacity
   
4. Redundant Name Servers
   Multiple geographically distributed servers
```

## Troubleshooting DNS Issues

### Common DNS Problems
```bash
# DNS Resolution Testing Tools

# 1. nslookup (basic queries)
nslookup www.example.com
nslookup www.example.com 8.8.8.8

# 2. dig (detailed information)
dig www.example.com
dig @8.8.8.8 www.example.com A
dig www.example.com ANY +short
dig +trace www.example.com    # Full resolution path

# 3. host (simple lookup)
host www.example.com
host -t MX example.com

# 4. systemd-resolve (Linux)
systemd-resolve www.example.com
systemd-resolve --statistics
systemd-resolve --flush-caches

# Windows-specific commands
ipconfig /displaydns          # Show DNS cache
ipconfig /flushdns            # Clear DNS cache  
nslookup -debug www.example.com

# Advanced troubleshooting
dig +trace +additional www.example.com
dig @ns1.example.com www.example.com  # Query authoritative
dig -x 192.0.2.1              # Reverse lookup
```

### DNS Error Codes
```
RCODE   Name                Description
─────────────────────────────────────────────────────────
0       NOERROR            No error
1       FORMERR            Format error in query
2       SERVFAIL           Server failure
3       NXDOMAIN           Domain name does not exist
4       NOTIMP             Query type not implemented
5       REFUSED            Query refused by server
6       YXDOMAIN           Domain name should not exist
7       YXRRSET            RRset should not exist
8       NXRRSET            RRset does not exist
9       NOTAUTH            Server not authoritative
10      NOTZONE            Name not in zone

Common Issues:
- NXDOMAIN: Domain doesn't exist or typo
- SERVFAIL: DNS server problem or DNSSEC failure
- REFUSED: Server policy blocks query
- Timeout: Network connectivity or server down
```

DNS is fundamental to internet infrastructure, providing the naming system that makes the web user-friendly while enabling efficient, distributed, and secure domain name resolution at global scale.
