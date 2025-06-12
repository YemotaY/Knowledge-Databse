# Security Protocols

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](./README.md)** | **🔒 [Security Protocols](./Security-Protocols.md)**

Security protocols provide the cryptographic and authentication mechanisms that enable secure communication across networks. This document covers the fundamental protocols that protect data in transit and at rest.

## Overview

Security protocols operate at multiple layers of the network stack to provide:
- **Confidentiality**: Protecting data from unauthorized access
- **Integrity**: Ensuring data hasn't been tampered with
- **Authentication**: Verifying the identity of communicating parties
- **Non-repudiation**: Preventing denial of communication
- **Perfect Forward Secrecy**: Protecting past communications even if keys are compromised

## Transport Layer Security (TLS)

### TLS Overview
TLS (Transport Layer Security) is the successor to SSL and provides secure communication over TCP connections.

### TLS Handshake Process
```
Client                                Server
  |                                     |
  |----------- ClientHello ----------->|
  |                                     |
  |<---------- ServerHello -------------|
  |<-------- Certificate --------------|
  |<---- ServerKeyExchange (optional)---|
  |<------- CertificateRequest ---------|
  |<---------- ServerHelloDone ---------|
  |                                     |
  |--------- Certificate ------------->|
  |------ ClientKeyExchange ---------->|
  |---- CertificateVerify (optional)-->|
  |------ ChangeCipherSpec ----------->|
  |----------- Finished -------------->|
  |                                     |
  |<----- ChangeCipherSpec -------------|
  |<---------- Finished ---------------|
  |                                     |
  |======= Encrypted Data Exchange =====|
```

### TLS Versions Comparison
```
Version    Year    Key Features
---------- ----    -------------
SSL 3.0    1996    • RC4, 3DES ciphers
                   • MD5, SHA-1 hashing
                   • Vulnerable to POODLE

TLS 1.0    1999    • Improved SSL 3.0
                   • HMAC message authentication
                   • Still uses MD5/SHA-1

TLS 1.1    2006    • Protection against CBC attacks
                   • Explicit initialization vectors
                   • Better random number generation

TLS 1.2    2008    • SHA-256 default hashing
                   • AES-GCM cipher suites
                   • Extensible PRF
                   • AEAD ciphers

TLS 1.3    2018    • 0-RTT handshake
                   • Perfect Forward Secrecy mandatory
                   • Simplified cipher suites
                   • Removed weak algorithms
```

### TLS Cipher Suites
```python
# Example TLS 1.2 cipher suite analysis
def parse_cipher_suite(suite_name):
    """
    Example: TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    """
    parts = suite_name.split('_')
    
    return {
        'protocol': parts[0],           # TLS
        'key_exchange': parts[1],       # ECDHE (Elliptic Curve Diffie-Hellman Ephemeral)
        'authentication': parts[2],     # RSA
        'encryption': f"{parts[4]}_{parts[5]}", # AES_256_GCM
        'mac': parts[6] if len(parts) > 6 else 'AEAD'  # SHA384 or AEAD
    }

# Modern recommended cipher suites
recommended_suites = [
    'TLS_AES_256_GCM_SHA384',          # TLS 1.3
    'TLS_CHACHA20_POLY1305_SHA256',     # TLS 1.3
    'TLS_AES_128_GCM_SHA256',          # TLS 1.3
    'TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384',    # TLS 1.2
    'TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256'  # TLS 1.2
]
```

### Certificate Management
```python
import ssl
import socket
from cryptography import x509
from cryptography.x509.oid import NameOID

def get_certificate_info(hostname, port=443):
    """Extract certificate information from a server"""
    context = ssl.create_default_context()
    
    with socket.create_connection((hostname, port)) as sock:
        with context.wrap_socket(sock, server_hostname=hostname) as ssock:
            # Get certificate in DER format
            der_cert_bin = ssock.getpeercert(binary_form=True)
            
    # Parse certificate
    cert = x509.load_der_x509_certificate(der_cert_bin)
    
    return {
        'subject': cert.subject.get_attributes_for_oid(NameOID.COMMON_NAME)[0].value,
        'issuer': cert.issuer.get_attributes_for_oid(NameOID.COMMON_NAME)[0].value,
        'not_valid_before': cert.not_valid_before,
        'not_valid_after': cert.not_valid_after,
        'signature_algorithm': cert.signature_algorithm_oid._name,
        'public_key_size': cert.public_key().key_size,
        'serial_number': cert.serial_number
    }

# Certificate pinning example
def verify_certificate_pinning(cert, expected_pin):
    """Verify certificate against expected pin"""
    import hashlib
    
    # Calculate SHA-256 fingerprint
    cert_sha256 = hashlib.sha256(cert.public_bytes()).hexdigest()
    return cert_sha256 == expected_pin
```

## IPSec (Internet Protocol Security)

### IPSec Architecture
IPSec provides security at the network layer, protecting all traffic between endpoints.

```
IPSec Components:
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│     AH      │    │     ESP     │    │     IKE     │
│ (Authentication│ │ (Encapsulating│ │ (Internet   │
│   Header)   │    │ Security    │    │ Key Exchange│
│             │    │  Payload)   │    │             │
└─────────────┘    └─────────────┘    └─────────────┘
      │                    │                    │
      └────────────────────┼────────────────────┘
                           │
                    ┌─────────────┐
                    │   Security  │
                    │ Association │
                    │    (SA)     │
                    └─────────────┘
```

### IPSec Modes
```python
# IPSec Transport Mode (end-to-end)
class TransportMode:
    """
    Original IP Header | IPSec Header | Original Payload
    
    - Protects payload only
    - Original IP header preserved
    - Used for end-to-end communication
    """
    def __init__(self):
        self.protects = "payload_only"
        self.header_modification = "minimal"
        self.use_case = "end_to_end"

# IPSec Tunnel Mode (gateway-to-gateway)
class TunnelMode:
    """
    New IP Header | IPSec Header | Original IP Header | Original Payload
    
    - Protects entire original IP packet
    - New IP header added
    - Used for VPN gateways
    """
    def __init__(self):
        self.protects = "entire_packet"
        self.header_modification = "complete_encapsulation"
        self.use_case = "site_to_site_vpn"
```

### Security Associations (SA)
```python
class SecurityAssociation:
    """IPSec Security Association configuration"""
    
    def __init__(self):
        self.spi = None          # Security Parameter Index
        self.destination_ip = None
        self.protocol = None     # AH or ESP
        
        # Cryptographic parameters
        self.encryption_algorithm = None
        self.encryption_key = None
        self.authentication_algorithm = None
        self.authentication_key = None
        
        # Lifetime parameters
        self.lifetime_seconds = 3600
        self.lifetime_bytes = 1000000
        
    def create_sa(self, config):
        """Create new Security Association"""
        return {
            'spi': self._generate_spi(),
            'encryption': config.get('encryption', 'AES-256-CBC'),
            'authentication': config.get('auth', 'HMAC-SHA256'),
            'pfs': config.get('perfect_forward_secrecy', True),
            'lifetime': config.get('lifetime', 3600)
        }
```

## VPN Protocols

### OpenVPN
```python
# OpenVPN configuration example
openvpn_config = """
client
dev tun
proto udp
remote vpn.example.com 1194

# Encryption settings
cipher AES-256-GCM
auth SHA384
key-direction 1

# Certificates
ca ca.crt
cert client.crt
key client.key
tls-auth ta.key 1

# Security enhancements
remote-cert-tls server
verify-x509-name vpn.example.com name
tls-version-min 1.2

# Connection settings
resolv-retry infinite
nobind
persist-key
persist-tun
verb 3
"""

def openvpn_security_analysis():
    """Analyze OpenVPN security features"""
    return {
        'encryption': {
            'data_channel': 'AES-256-GCM',
            'control_channel': 'TLS 1.2+',
            'key_size': 256
        },
        'authentication': {
            'hmac': 'SHA384',
            'certificates': 'X.509',
            'tls_auth': 'HMAC-based'
        },
        'key_exchange': {
            'method': 'TLS handshake',
            'perfect_forward_secrecy': True,
            'renegotiation': 'periodic'
        }
    }
```

### WireGuard
```python
# WireGuard configuration
class WireGuardConfig:
    """Modern VPN protocol with simplified configuration"""
    
    def __init__(self):
        self.crypto_primitives = {
            'key_exchange': 'Curve25519',
            'encryption': 'ChaCha20Poly1305',
            'hashing': 'BLAKE2s',
            'keyed_hash': 'BLAKE2s + Poly1305'
        }
    
    def generate_config(self, private_key, peer_public_key, endpoint):
        return f"""
[Interface]
PrivateKey = {private_key}
Address = 10.0.0.2/32
DNS = 1.1.1.1

[Peer]
PublicKey = {peer_public_key}
Endpoint = {endpoint}:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
"""

# WireGuard protocol analysis
def wireguard_vs_openvpn():
    return {
        'wireguard': {
            'lines_of_code': '~4000',
            'crypto_primitives': 4,
            'handshake_messages': 4,
            'performance': 'High',
            'battery_usage': 'Low'
        },
        'openvpn': {
            'lines_of_code': '~70000',
            'crypto_options': 'Many',
            'configuration_complexity': 'High',
            'performance': 'Moderate',
            'compatibility': 'Excellent'
        }
    }
```

## SSH (Secure Shell)

### SSH Protocol Architecture
```
SSH Protocol Stack:
┌─────────────────────────────────────┐
│           SSH Connection            │  ← Multiplexing, flow control
├─────────────────────────────────────┤
│         SSH Authentication          │  ← User authentication
├─────────────────────────────────────┤
│          SSH Transport              │  ← Encryption, integrity
├─────────────────────────────────────┤
│              TCP                    │  ← Reliable transport
└─────────────────────────────────────┘
```

### SSH Key Exchange
```python
def ssh_key_exchange_process():
    """SSH-2 key exchange process"""
    return {
        'phase_1': {
            'name': 'Protocol Version Exchange',
            'purpose': 'Agree on SSH version',
            'messages': ['SSH-2.0-ClientVersion', 'SSH-2.0-ServerVersion']
        },
        'phase_2': {
            'name': 'Algorithm Negotiation',
            'purpose': 'Select encryption/MAC algorithms',
            'client_sends': 'SSH_MSG_KEXINIT',
            'server_sends': 'SSH_MSG_KEXINIT'
        },
        'phase_3': {
            'name': 'Key Exchange',
            'purpose': 'Generate session keys',
            'methods': ['diffie-hellman-group14-sha256', 'ecdh-sha2-nistp256']
        },
        'phase_4': {
            'name': 'Service Request',
            'purpose': 'Request SSH services',
            'common_services': ['ssh-userauth', 'ssh-connection']
        }
    }

# SSH client configuration
ssh_client_config = """
Host secure-server
    HostName example.com
    Port 22
    User myuser
    
    # Key-based authentication
    IdentityFile ~/.ssh/id_rsa
    PubkeyAuthentication yes
    PasswordAuthentication no
    
    # Security settings
    Protocol 2
    Ciphers aes256-gcm@openssh.com,aes128-gcm@openssh.com
    MACs hmac-sha2-256,hmac-sha2-512
    KexAlgorithms ecdh-sha2-nistp384,ecdh-sha2-nistp256
    
    # Connection settings
    ServerAliveInterval 60
    ServerAliveCountMax 3
    ConnectTimeout 10
"""
```

### SSH Tunneling
```python
import paramiko
import threading
import select
import socket

class SSHTunnel:
    """SSH tunneling implementation"""
    
    def __init__(self, ssh_host, ssh_port, ssh_user, ssh_key_file):
        self.ssh_host = ssh_host
        self.ssh_port = ssh_port
        self.ssh_user = ssh_user
        self.ssh_key_file = ssh_key_file
        self.client = None
    
    def connect(self):
        """Establish SSH connection"""
        self.client = paramiko.SSHClient()
        self.client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        
        private_key = paramiko.RSAKey.from_private_key_file(self.ssh_key_file)
        
        self.client.connect(
            hostname=self.ssh_host,
            port=self.ssh_port,
            username=self.ssh_user,
            pkey=private_key,
            compression=True
        )
    
    def local_forward(self, local_port, remote_host, remote_port):
        """Create local port forwarding"""
        transport = self.client.get_transport()
        
        def forward_tunnel(local_sock):
            try:
                remote_sock = transport.open_channel(
                    'direct-tcpip',
                    (remote_host, remote_port),
                    local_sock.getpeername()
                )
                
                # Relay data between sockets
                while True:
                    ready, _, _ = select.select([local_sock, remote_sock], [], [])
                    
                    if local_sock in ready:
                        data = local_sock.recv(4096)
                        if not data:
                            break
                        remote_sock.send(data)
                    
                    if remote_sock in ready:
                        data = remote_sock.recv(4096)
                        if not data:
                            break
                        local_sock.send(data)
                        
            except Exception as e:
                print(f"Tunnel error: {e}")
            finally:
                local_sock.close()
                remote_sock.close()
        
        # Setup local listener
        server_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        server_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        server_sock.bind(('localhost', local_port))
        server_sock.listen(5)
        
        print(f"Forwarding localhost:{local_port} -> {remote_host}:{remote_port}")
        
        while True:
            local_sock, addr = server_sock.accept()
            thread = threading.Thread(target=forward_tunnel, args=(local_sock,))
            thread.daemon = True
            thread.start()

# SSH tunnel usage example
def create_database_tunnel():
    """Create secure tunnel to database server"""
    tunnel = SSHTunnel(
        ssh_host='bastion.example.com',
        ssh_port=22,
        ssh_user='admin',
        ssh_key_file='~/.ssh/bastion_key'
    )
    
    tunnel.connect()
    tunnel.local_forward(
        local_port=5432,
        remote_host='db.internal.example.com',
        remote_port=5432
    )
```

## HTTPS and Certificate Authorities

### Certificate Authority Hierarchy
```
Root CA (Offline, Hardware Security Module)
    │
    ├── Intermediate CA 1 (Online, Regular Operations)
    │   ├── End Entity Cert (example.com)
    │   ├── End Entity Cert (api.example.com)
    │   └── End Entity Cert (mail.example.com)
    │
    └── Intermediate CA 2 (Cross-signed, Compatibility)
        ├── End Entity Cert (subdomain.example.com)
        └── End Entity Cert (service.example.com)
```

### Certificate Validation Process
```python
import ssl
import socket
from cryptography import x509
from cryptography.x509.verification import PolicyBuilder, StoreBuilder

class CertificateValidator:
    """Certificate chain validation"""
    
    def __init__(self):
        self.trusted_roots = self._load_system_roots()
    
    def validate_certificate_chain(self, hostname, port=443):
        """Validate complete certificate chain"""
        # Get certificate chain
        context = ssl.create_default_context()
        context.check_hostname = False
        context.verify_mode = ssl.CERT_NONE
        
        with socket.create_connection((hostname, port)) as sock:
            with context.wrap_socket(sock, server_hostname=hostname) as ssock:
                cert_chain = ssock.getpeercert_chain()
        
        # Validate each certificate in chain
        validation_results = []
        
        for i, cert_der in enumerate(cert_chain):
            cert = x509.load_der_x509_certificate(cert_der)
            
            validation = {
                'position': i,
                'subject': cert.subject.rfc4514_string(),
                'issuer': cert.issuer.rfc4514_string(),
                'validity': self._check_validity_period(cert),
                'signature': self._verify_signature(cert, cert_chain),
                'revocation': self._check_revocation_status(cert),
                'key_usage': self._check_key_usage(cert),
                'san': self._check_subject_alt_names(cert, hostname)
            }
            
            validation_results.append(validation)
        
        return validation_results
    
    def _check_validity_period(self, cert):
        """Check certificate validity period"""
        from datetime import datetime, timezone
        
        now = datetime.now(timezone.utc)
        return {
            'not_before': cert.not_valid_before,
            'not_after': cert.not_valid_after,
            'currently_valid': cert.not_valid_before <= now <= cert.not_valid_after,
            'days_remaining': (cert.not_valid_after - now).days
        }
    
    def _check_revocation_status(self, cert):
        """Check certificate revocation status (OCSP/CRL)"""
        try:
            # Extract OCSP responder URL
            aia_ext = cert.extensions.get_extension_for_oid(
                x509.oid.ExtensionOID.AUTHORITY_INFORMATION_ACCESS
            )
            
            ocsp_urls = []
            for access_desc in aia_ext.value:
                if access_desc.access_method == x509.oid.AuthorityInformationAccessOID.OCSP:
                    ocsp_urls.append(access_desc.access_location.value)
            
            # Simplified OCSP check (in practice, use proper OCSP library)
            return {
                'method': 'OCSP',
                'responder_urls': ocsp_urls,
                'status': 'not_revoked'  # Placeholder
            }
            
        except x509.ExtensionNotFound:
            return {'method': 'none', 'status': 'unknown'}

# HSTS (HTTP Strict Transport Security) implementation
def implement_hsts():
    """HSTS header implementation"""
    return {
        'header': 'Strict-Transport-Security',
        'example_values': [
            'max-age=31536000',  # 1 year
            'max-age=31536000; includeSubDomains',
            'max-age=31536000; includeSubDomains; preload'
        ],
        'best_practices': {
            'max_age': 'At least 6 months (15768000 seconds)',
            'include_subdomains': 'Use when all subdomains support HTTPS',
            'preload': 'Submit to browser preload lists',
            'testing': 'Start with shorter max-age for testing'
        }
    }
```

## Modern Security Protocols

### DNS over HTTPS (DoH) and DNS over TLS (DoT)
```python
import ssl
import socket
import json
import base64
import requests

class SecureDNS:
    """Secure DNS protocols implementation"""
    
    def __init__(self):
        self.doh_servers = [
            'https://cloudflare-dns.com/dns-query',
            'https://dns.google/dns-query',
            'https://dns.quad9.net/dns-query'
        ]
        
        self.dot_servers = [
            ('1.1.1.1', 853),
            ('8.8.8.8', 853),
            ('9.9.9.9', 853)
        ]
    
    def dns_over_https(self, domain, record_type='A'):
        """DNS over HTTPS query"""
        for server in self.doh_servers:
            try:
                params = {
                    'name': domain,
                    'type': record_type,
                    'cd': 'false',  # Checking disabled
                    'do': 'true'    # DNSSEC OK
                }
                
                headers = {
                    'Accept': 'application/dns-json',
                    'User-Agent': 'SecureDNS/1.0'
                }
                
                response = requests.get(
                    server,
                    params=params,
                    headers=headers,
                    timeout=5
                )
                
                if response.status_code == 200:
                    dns_response = response.json()
                    return self._parse_dns_response(dns_response)
                    
            except Exception as e:
                print(f"DoH query failed for {server}: {e}")
                continue
        
        return None
    
    def dns_over_tls(self, domain, record_type='A'):
        """DNS over TLS query"""
        import dns.message
        import dns.query
        
        for server_ip, port in self.dot_servers:
            try:
                # Create DNS query
                query = dns.message.make_query(domain, record_type)
                
                # Send over TLS
                response = dns.query.tls(query, server_ip, port=port)
                
                return self._parse_dns_response(response)
                
            except Exception as e:
                print(f"DoT query failed for {server_ip}: {e}")
                continue
        
        return None

# DNSSEC validation
class DNSSECValidator:
    """DNSSEC signature validation"""
    
    def validate_dnssec_chain(self, domain):
        """Validate DNSSEC chain of trust"""
        import dns.resolver
        import dns.dnssec
        
        try:
            # Get DNSKEY records
            dnskey_response = dns.resolver.resolve(domain, 'DNSKEY')
            
            # Get DS records from parent zone
            parent_zone = '.'.join(domain.split('.')[1:])
            ds_response = dns.resolver.resolve(domain, 'DS')
            
            # Validate chain
            validation_result = {
                'domain': domain,
                'dnskey_records': len(dnskey_response),
                'ds_records': len(ds_response),
                'algorithms': [],
                'valid_signatures': 0
            }
            
            for rr in dnskey_response:
                validation_result['algorithms'].append(rr.algorithm)
            
            return validation_result
            
        except Exception as e:
            return {'error': str(e), 'valid': False}
```

### Zero Trust Network Access (ZTNA)
```python
class ZeroTrustArchitecture:
    """Zero Trust security model implementation"""
    
    def __init__(self):
        self.principles = {
            'verify_explicitly': 'Always authenticate and authorize',
            'least_privilege': 'Limit user access with Just-In-Time',
            'assume_breach': 'Verify end-to-end encryption'
        }
    
    def device_trust_evaluation(self, device_info):
        """Evaluate device trustworthiness"""
        trust_score = 0
        
        # Device compliance checks
        compliance_checks = {
            'os_version': device_info.get('os_updated', False),
            'antivirus': device_info.get('av_enabled', False),
            'encryption': device_info.get('disk_encrypted', False),
            'certificate': device_info.get('device_cert_valid', False),
            'patch_level': device_info.get('patches_current', False)
        }
        
        for check, passed in compliance_checks.items():
            if passed:
                trust_score += 20
        
        # Behavioral analysis
        behavior_factors = {
            'location_anomaly': device_info.get('unusual_location', False),
            'time_anomaly': device_info.get('unusual_time', False),
            'access_pattern': device_info.get('normal_pattern', True)
        }
        
        for factor, is_normal in behavior_factors.items():
            if factor.endswith('_anomaly') and is_normal:
                trust_score -= 15
            elif factor == 'access_pattern' and is_normal:
                trust_score += 10
        
        return {
            'trust_score': max(0, min(100, trust_score)),
            'compliance': compliance_checks,
            'behavior': behavior_factors,
            'recommendation': self._get_access_recommendation(trust_score)
        }
    
    def _get_access_recommendation(self, score):
        """Get access recommendation based on trust score"""
        if score >= 80:
            return 'full_access'
        elif score >= 60:
            return 'limited_access'
        elif score >= 40:
            return 'restricted_access'
        else:
            return 'deny_access'

# Mutual TLS (mTLS) implementation
def setup_mutual_tls():
    """Configure mutual TLS authentication"""
    return {
        'server_config': {
            'verify_mode': 'CERT_REQUIRED',
            'ca_certs': '/path/to/ca-bundle.pem',
            'certfile': '/path/to/server.crt',
            'keyfile': '/path/to/server.key',
            'client_cert_validation': True
        },
        'client_config': {
            'ca_certs': '/path/to/ca-bundle.pem',
            'certfile': '/path/to/client.crt',
            'keyfile': '/path/to/client.key',
            'verify_server': True,
            'check_hostname': True
        },
        'benefits': [
            'Bidirectional authentication',
            'Strong client identity verification',
            'Protection against man-in-the-middle attacks',
            'Compliance with zero-trust principles'
        ]
    }
```

## Performance and Optimization

### TLS Performance Optimization
```python
def tls_performance_optimization():
    """TLS optimization strategies"""
    return {
        'handshake_optimization': {
            'session_resumption': {
                'session_ids': 'Server maintains session cache',
                'session_tickets': 'Client stores encrypted session state',
                'tls_1_3_psk': 'Pre-shared keys for 0-RTT'
            },
            'certificate_optimization': {
                'ecdsa_certificates': 'Smaller than RSA certificates',
                'certificate_compression': 'Reduce certificate size',
                'ocsp_stapling': 'Include OCSP response in handshake'
            }
        },
        
        'cipher_optimization': {
            'aead_ciphers': 'AES-GCM, ChaCha20-Poly1305',
            'hardware_acceleration': 'AES-NI, cryptographic coprocessors',
            'cipher_preference': 'Order by performance vs security'
        },
        
        'protocol_optimization': {
            'tls_1_3': 'Reduced handshake round trips',
            'http2_with_tls': 'Multiplexed connections',
            'early_data': '0-RTT application data'
        }
    }

# TLS connection pooling
class TLSConnectionPool:
    """Manage persistent TLS connections"""
    
    def __init__(self, max_connections=10):
        self.max_connections = max_connections
        self.connections = {}
        self.connection_stats = {}
    
    def get_connection(self, hostname, port=443):
        """Get or create TLS connection"""
        key = f"{hostname}:{port}"
        
        if key in self.connections:
            conn = self.connections[key]
            if self._is_connection_healthy(conn):
                self.connection_stats[key]['reused'] += 1
                return conn
            else:
                del self.connections[key]
        
        # Create new connection
        conn = self._create_tls_connection(hostname, port)
        self.connections[key] = conn
        self.connection_stats[key] = {
            'created': time.time(),
            'reused': 0,
            'bytes_sent': 0,
            'bytes_received': 0
        }
        
        return conn
    
    def _create_tls_connection(self, hostname, port):
        """Create optimized TLS connection"""
        context = ssl.create_default_context()
        
        # Optimize TLS settings
        context.set_ciphers('ECDHE+AESGCM:ECDHE+CHACHA20:DHE+AESGCM')
        context.options |= ssl.OP_NO_SSLv2 | ssl.OP_NO_SSLv3
        context.set_ecdh_curve('prime256v1')
        
        sock = socket.create_connection((hostname, port))
        tls_sock = context.wrap_socket(sock, server_hostname=hostname)
        
        return tls_sock
```

## Security Best Practices

### Protocol Selection Guidelines
```python
def security_protocol_recommendations():
    """Current security protocol recommendations"""
    return {
        'web_traffic': {
            'recommended': 'TLS 1.3 with HSTS',
            'minimum': 'TLS 1.2',
            'deprecated': 'TLS 1.0, TLS 1.1, SSL 3.0',
            'cipher_suites': [
                'TLS_AES_256_GCM_SHA384',
                'TLS_CHACHA20_POLY1305_SHA256',
                'TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384'
            ]
        },
        
        'vpn_access': {
            'recommended': 'WireGuard for modern setups',
            'enterprise': 'IPSec with IKEv2',
            'legacy_support': 'OpenVPN with modern ciphers',
            'avoid': 'PPTP, L2TP without IPSec'
        },
        
        'remote_access': {
            'recommended': 'SSH with certificate authentication',
            'minimum': 'SSH with RSA 2048-bit keys',
            'deprecated': 'Telnet, RSH, SSH with DSA keys',
            'enhancements': 'SSH CA, bastions hosts, tunneling'
        },
        
        'email_security': {
            'transport': 'SMTP with STARTTLS',
            'authentication': 'SASL with OAuth2',
            'anti_spam': 'SPF, DKIM, DMARC',
            'encryption': 'S/MIME or PGP for content'
        }
    }

# Security monitoring and alerting
class SecurityMonitor:
    """Monitor security protocol usage and anomalies"""
    
    def __init__(self):
        self.alerts = []
        self.baseline_metrics = {}
    
    def analyze_tls_connections(self, connections_log):
        """Analyze TLS connection patterns"""
        analysis = {
            'protocol_versions': {},
            'cipher_suites': {},
            'certificate_issues': [],
            'anomalies': []
        }
        
        for conn in connections_log:
            # Track protocol versions
            version = conn.get('tls_version')
            analysis['protocol_versions'][version] = \
                analysis['protocol_versions'].get(version, 0) + 1
            
            # Track cipher suites
            cipher = conn.get('cipher_suite')
            analysis['cipher_suites'][cipher] = \
                analysis['cipher_suites'].get(cipher, 0) + 1
            
            # Check for weak configurations
            if version in ['TLSv1.0', 'TLSv1.1', 'SSLv3']:
                analysis['anomalies'].append({
                    'type': 'weak_protocol',
                    'details': f"Deprecated protocol: {version}",
                    'timestamp': conn.get('timestamp'),
                    'client_ip': conn.get('client_ip')
                })
            
            # Check certificate validity
            if conn.get('cert_expired'):
                analysis['certificate_issues'].append({
                    'type': 'expired_certificate',
                    'hostname': conn.get('hostname'),
                    'expiry_date': conn.get('cert_expiry')
                })
        
        return analysis
    
    def generate_security_report(self, timeframe='24h'):
        """Generate comprehensive security report"""
        return {
            'timeframe': timeframe,
            'summary': {
                'total_connections': 0,
                'secure_connections': 0,
                'weak_configurations': 0,
                'blocked_attempts': 0
            },
            'recommendations': [
                'Disable TLS 1.0 and 1.1',
                'Enable HSTS with preload',
                'Implement certificate transparency monitoring',
                'Regular security certificate rotation'
            ]
        }
```

## Troubleshooting and Debugging

### Common Security Protocol Issues
```python
def diagnose_tls_issues():
    """Common TLS/SSL troubleshooting scenarios"""
    return {
        'handshake_failures': {
            'cipher_mismatch': {
                'symptoms': 'SSL_ERROR_NO_CYPHER_OVERLAP',
                'cause': 'No common cipher suites',
                'solution': 'Update cipher suite configuration'
            },
            'protocol_mismatch': {
                'symptoms': 'SSL_ERROR_PROTOCOL_VERSION_ALERT',
                'cause': 'Client/server protocol version incompatibility',
                'solution': 'Enable compatible TLS versions'
            },
            'certificate_issues': {
                'symptoms': 'SSL_ERROR_BAD_CERT_DOMAIN',
                'cause': 'Certificate hostname mismatch',
                'solution': 'Use correct certificate or SAN entries'
            }
        },
        
        'performance_issues': {
            'slow_handshake': {
                'causes': ['Large certificate chains', 'CPU bottlenecks', 'Network latency'],
                'solutions': ['OCSP stapling', 'Session resumption', 'Hardware acceleration']
            },
            'connection_drops': {
                'causes': ['Idle timeouts', 'Load balancer issues', 'Certificate expiry'],
                'solutions': ['Keep-alive settings', 'Health checks', 'Monitoring alerts']
            }
        }
    }

# SSL/TLS debugging tools
def debug_tls_connection(hostname, port=443):
    """Debug TLS connection issues"""
    import ssl
    import socket
    
    debug_info = {
        'hostname': hostname,
        'port': port,
        'connection_test': None,
        'certificate_info': None,
        'cipher_info': None,
        'protocol_info': None
    }
    
    try:
        # Test basic connectivity
        sock = socket.create_connection((hostname, port), timeout=10)
        debug_info['connection_test'] = 'SUCCESS'
        
        # Test TLS handshake
        context = ssl.create_default_context()
        with context.wrap_socket(sock, server_hostname=hostname) as tls_sock:
            # Get connection details
            debug_info['cipher_info'] = tls_sock.cipher()
            debug_info['protocol_info'] = tls_sock.version()
            
            # Get certificate information
            cert = tls_sock.getpeercert()
            debug_info['certificate_info'] = {
                'subject': cert.get('subject'),
                'issuer': cert.get('issuer'),
                'version': cert.get('version'),
                'serial_number': cert.get('serialNumber'),
                'not_before': cert.get('notBefore'),
                'not_after': cert.get('notAfter')
            }
            
    except Exception as e:
        debug_info['error'] = str(e)
        debug_info['connection_test'] = 'FAILED'
    
    return debug_info
```

## Further Reading

- **Standards Documentation**
  - [RFC 8446 - TLS 1.3](https://tools.ietf.org/html/rfc8446)
  - [RFC 4301 - IPSec Architecture](https://tools.ietf.org/html/rfc4301)
  - [RFC 4253 - SSH Transport Layer](https://tools.ietf.org/html/rfc4253)

- **Related Protocols**
  - [TCP-IP-Stack](TCP-IP-Stack.md) - Network layer foundations
  - [HTTP-and-HTTPS](HTTP-and-HTTPS.md) - Web protocol security
  - [Email-Protocols](Email-Protocols.md) - Email security protocols

- **Modern Software**
  - [Modern Software](../06-Modern-Software/) - Cloud security and DevOps
  - [API Design](../06-Modern-Software/API-Design.md) - API security practices

Security protocols form the backbone of modern network communication, providing the trust and protection necessary for digital interactions in an interconnected world.
