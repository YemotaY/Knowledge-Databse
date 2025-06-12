# HTTP and HTTPS Protocols

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

HTTP (HyperText Transfer Protocol) and HTTPS (HTTP Secure) are the foundation of web communication, enabling browsers and servers to exchange web pages, APIs, and multimedia content across the internet.

## Overview

HTTP is an application-layer protocol that follows a client-server model, where clients (typically web browsers) send requests to servers, which respond with the requested resources. HTTPS adds a security layer using TLS/SSL encryption.

## HTTP Fundamentals

### HTTP Request Structure
```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Cache-Control: max-age=0

[Optional request body]
```

**Request Components:**
- **Request Line**: Method, URI, HTTP version
- **Headers**: Metadata about the request
- **Body**: Data payload (for POST, PUT, etc.)

### HTTP Response Structure
```
HTTP/1.1 200 OK
Date: Wed, 12 Jun 2025 10:30:00 GMT
Server: Apache/2.4.41
Content-Type: text/html; charset=UTF-8
Content-Length: 1234
Last-Modified: Tue, 11 Jun 2025 15:20:00 GMT
ETag: "abc123"
Cache-Control: public, max-age=3600

<!DOCTYPE html>
<html>
<head><title>Example Page</title></head>
<body><h1>Hello World!</h1></body>
</html>
```

**Response Components:**
- **Status Line**: HTTP version, status code, reason phrase
- **Headers**: Metadata about the response
- **Body**: The actual content (HTML, JSON, etc.)

## HTTP Methods

### Standard HTTP Methods
```
Method   Description              Safe  Idempotent  Cacheable
──────────────────────────────────────────────────────────────
GET      Retrieve resource        Yes   Yes         Yes
HEAD     Get headers only         Yes   Yes         Yes
POST     Submit data              No    No          Rarely
PUT      Create/update resource   No    Yes         No
PATCH    Partial update           No    No          No
DELETE   Remove resource          No    Yes         No
OPTIONS  Get allowed methods      Yes   Yes         No
TRACE    Diagnostic loopback      Yes   Yes         No
CONNECT  Establish tunnel         No    No          No

Safe: No side effects on server
Idempotent: Multiple identical requests have same effect
Cacheable: Response can be stored and reused
```

### Method Usage Examples
```http
# GET - Retrieve user information
GET /api/users/123 HTTP/1.1
Host: api.example.com

# POST - Create new user
POST /api/users HTTP/1.1
Content-Type: application/json
Content-Length: 85

{
  "name": "John Doe",
  "email": "john@example.com",
  "role": "user"
}

# PUT - Update entire user record
PUT /api/users/123 HTTP/1.1
Content-Type: application/json

{
  "id": 123,
  "name": "John Smith",
  "email": "john.smith@example.com",
  "role": "admin"
}

# PATCH - Partial update
PATCH /api/users/123 HTTP/1.1
Content-Type: application/json

{
  "email": "newemail@example.com"
}

# DELETE - Remove user
DELETE /api/users/123 HTTP/1.1
```

## HTTP Status Codes

### Status Code Categories
```
1xx Informational    2xx Success         3xx Redirection
────────────────     ───────────────     ─────────────────
100 Continue         200 OK              300 Multiple Choices
101 Switching Proto  201 Created         301 Moved Permanently
102 Processing       202 Accepted        302 Found (Temporary)
                     204 No Content      304 Not Modified
                     206 Partial Content 307 Temporary Redirect
                                        308 Permanent Redirect

4xx Client Error     5xx Server Error
──────────────────   ────────────────
400 Bad Request      500 Internal Server Error
401 Unauthorized     501 Not Implemented
403 Forbidden        502 Bad Gateway
404 Not Found        503 Service Unavailable
405 Method Not Allow 504 Gateway Timeout
408 Request Timeout  505 HTTP Version Not Supported
409 Conflict
411 Length Required
429 Too Many Requests
```

### Common Status Code Usage
```
Scenario                           Status Code    Response
─────────────────────────────────────────────────────────
User successfully retrieved       200 OK         User data
User created successfully         201 Created    User data + Location header
User updated, no response body    204 No Content Empty body
Resource moved permanently        301 Moved      New URL in Location header
Resource not modified             304 Not Mod    Empty body (use cached)
Invalid request format            400 Bad Req    Error message
Authentication required           401 Unauth     WWW-Authenticate header
Access denied                     403 Forbidden  Error message  
Resource doesn't exist            404 Not Found  Error message
Server overloaded                 503 Unavail    Retry-After header
```

## HTTP Headers

### Common Request Headers
```
Header                 Purpose                           Example
─────────────────────────────────────────────────────────────────────
Host                   Target server                     Host: www.example.com
User-Agent            Client identification             User-Agent: Chrome/91.0
Accept                Acceptable content types          Accept: application/json
Accept-Language       Preferred languages               Accept-Language: en-US,en
Accept-Encoding       Supported compressions            Accept-Encoding: gzip
Authorization         Authentication credentials        Authorization: Bearer token123
Content-Type          Request body format               Content-Type: application/json
Content-Length        Request body size                 Content-Length: 1234
Cache-Control         Caching directives               Cache-Control: no-cache
If-Modified-Since     Conditional request              If-Modified-Since: Wed, 21 Oct 2015
```

### Common Response Headers
```
Header                 Purpose                           Example
─────────────────────────────────────────────────────────────────────
Content-Type          Response body format              Content-Type: text/html
Content-Length        Response body size                Content-Length: 5678
Content-Encoding      Response compression              Content-Encoding: gzip
Cache-Control         Caching directives               Cache-Control: max-age=3600
ETag                  Resource version identifier      ETag: "abc123def456"
Last-Modified         Last modification time           Last-Modified: Wed, 21 Oct 2015
Location              Redirect target                  Location: /new-url
Set-Cookie            Set client cookie                Set-Cookie: sessionid=abc123
Access-Control-*      CORS headers                     Access-Control-Allow-Origin: *
```

## HTTP Versions

### HTTP/1.0 vs HTTP/1.1 vs HTTP/2 vs HTTP/3
```
Feature               HTTP/1.0    HTTP/1.1    HTTP/2      HTTP/3
────────────────────────────────────────────────────────────────────
Connection Reuse      No          Yes         Yes         Yes
Multiplexing          No          No          Yes         Yes
Header Compression    No          No          Yes         Yes
Server Push           No          No          Yes         No
Binary Protocol       No          No          Yes         Yes
Transport Protocol    TCP         TCP         TCP         QUIC/UDP
Stream Priority       No          No          Yes         Yes
Flow Control          TCP only    TCP only    Per stream  Per stream
```

### HTTP/2 Features
```
HTTP/1.1 (6 requests):               HTTP/2 (single connection):
Client ←→ Server                      Client ←→ Server
  │         │                           │    ║ Stream 1 (HTML)
  │── GET ──│ ← HTML                     │    ║ Stream 3 (CSS)
  │         │                           │    ║ Stream 5 (JS)
  │── GET ──│ ← CSS                      │    ║ Stream 7 (Image)
  │         │                           │    ║ Stream 9 (Font)
  │── GET ──│ ← JS                       │    ║ Stream 11 (API)
  │         │                           │    ║
  │── GET ──│ ← Image                    │ All multiplexed on single
  │         │                           │ TCP connection
  │── GET ──│ ← Font
  │         │
  │── GET ──│ ← API data

Benefits:
- Multiplexing: Multiple streams on one connection
- Header Compression: HPACK reduces overhead
- Server Push: Server sends resources before requested
- Binary Framing: More efficient than text
```

### HTTP/3 and QUIC
```
HTTP/3 Stack:                    vs    HTTP/2 Stack:
┌─────────────────────┐              ┌─────────────────────┐
│       HTTP/3        │              │       HTTP/2        │
├─────────────────────┤              ├─────────────────────┤
│        QUIC         │              │        TLS          │
├─────────────────────┤              ├─────────────────────┤
│        UDP          │              │        TCP          │
├─────────────────────┤              ├─────────────────────┤
│        IP           │              │        IP           │
└─────────────────────┘              └─────────────────────┘

QUIC Benefits:
- 0-RTT connection establishment
- Built-in encryption
- Stream-level flow control
- No head-of-line blocking
- Connection migration (IP changes)
```

## HTTPS and Security

### TLS Handshake Process
```
Client                              Server
  │                                   │
  │──── ClientHello ─────────────────→│ 1. Cipher suites, random
  │                                   │
  │←─── ServerHello ─────────────────│ 2. Selected cipher, random
  │←─── Certificate ─────────────────│ 3. Server certificate
  │←─── ServerHelloDone ─────────────│ 4. Handshake done
  │                                   │
  │──── ClientKeyExchange ──────────→│ 5. Pre-master secret
  │──── ChangeCipherSpec ───────────→│ 6. Switch to encrypted
  │──── Finished ───────────────────→│ 7. Handshake verification
  │                                   │
  │←─── ChangeCipherSpec ────────────│ 8. Server switches
  │←─── Finished ────────────────────│ 9. Handshake complete
  │                                   │
  │ ═══ Encrypted Communication ═══ │
```

### SSL/TLS Versions
```
Version    Released    Status                Security Level
─────────────────────────────────────────────────────────────
SSL 1.0    Never       Never released        N/A
SSL 2.0    1995        Deprecated (2011)     Insecure
SSL 3.0    1996        Deprecated (2015)     Vulnerable (POODLE)
TLS 1.0    1999        Deprecated (2020)     Weak
TLS 1.1    2006        Deprecated (2020)     Weak
TLS 1.2    2008        Widely supported      Secure
TLS 1.3    2018        Modern standard       Most secure

Current Recommendation: TLS 1.2 minimum, TLS 1.3 preferred
```

### Certificate Validation
```
Certificate Chain Verification:

Root CA Certificate (Self-signed)
        ↓ Signs
Intermediate CA Certificate
        ↓ Signs  
Server Certificate (example.com)

Validation Steps:
1. Check certificate validity dates
2. Verify domain name matches
3. Validate certificate chain
4. Check certificate revocation (CRL/OCSP)
5. Verify cryptographic signatures

Certificate Information:
Subject: CN=example.com, O=Company, C=US
Issuer: CN=CA Authority, O=CA Corp
Valid: 2025-01-01 to 2026-01-01
SANs: example.com, www.example.com, api.example.com
```

## Caching

### Cache-Control Directives
```
Directive              Purpose                        Example
────────────────────────────────────────────────────────────────
max-age=seconds        Cache lifetime                 max-age=3600
no-cache               Must revalidate                no-cache
no-store               Never cache                    no-store
private                Only client cache              private
public                 Any cache allowed              public
must-revalidate        Strict revalidation           must-revalidate
immutable              Never changes                  immutable

Response Examples:
Cache-Control: public, max-age=31536000, immutable    # Static assets
Cache-Control: private, max-age=0, no-cache          # User data  
Cache-Control: public, max-age=3600                  # API responses
Cache-Control: no-store                              # Sensitive data
```

### HTTP Caching Flow
```
Browser Request Flow:

1. Check Browser Cache
   ├─ Fresh? → Use cached response
   └─ Stale? → Continue to step 2

2. Conditional Request (if ETag/Last-Modified)
   ├─ If-None-Match: "etag-value"
   └─ If-Modified-Since: date

3. Server Response
   ├─ 304 Not Modified → Use cached response
   └─ 200 OK → New response + cache

Cache Hierarchy:
Browser Cache → Proxy Cache → CDN → Origin Server
     ↑              ↑          ↑         ↑
   Fastest      Fast cache  Global     Source
   (local)      (network)   (edge)    (origin)
```

## Content Negotiation

### Accept Headers
```
Content Negotiation Examples:

# Content Type Negotiation
Accept: application/json, application/xml;q=0.9, text/plain;q=0.8

Server responses:
application/json (q=1.0, highest priority)
application/xml  (q=0.9, medium priority)  
text/plain       (q=0.8, lowest priority)

# Language Negotiation
Accept-Language: en-US,en;q=0.9,es;q=0.8,fr;q=0.7

# Encoding Negotiation  
Accept-Encoding: gzip, deflate, br;q=0.9, *;q=0.8

# Character Set Negotiation
Accept-Charset: utf-8, iso-8859-1;q=0.8, *;q=0.7

Quality Values (q):
- 1.0 = highest preference (default)
- 0.9 = high preference
- 0.8 = medium preference
- 0.0 = not acceptable
```

## REST API Design

### RESTful URL Patterns
```
Resource         GET              POST           PUT              DELETE
─────────────────────────────────────────────────────────────────────────
Collection       List all         Create new     Replace all      Delete all
/users           GET /users       POST /users    PUT /users       DELETE /users

Resource         Get one          Error          Replace one      Delete one  
/users/123       GET /users/123   405 Error     PUT /users/123   DELETE /users/123

Sub-collection   List related     Create related Replace related  Delete related
/users/123/posts GET .../posts    POST .../posts PUT .../posts    DELETE .../posts

Sub-resource     Get related      Error          Replace related  Delete related
/users/123/profile GET .../profile 405 Error    PUT .../profile  DELETE .../profile
```

### REST API Example
```http
# Get all users (paginated)
GET /api/v1/users?page=1&limit=20&sort=name HTTP/1.1
Accept: application/json

Response:
{
  "data": [
    {"id": 1, "name": "John Doe", "email": "john@example.com"},
    {"id": 2, "name": "Jane Smith", "email": "jane@example.com"}
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "pages": 8
  },
  "links": {
    "self": "/api/v1/users?page=1&limit=20",
    "next": "/api/v1/users?page=2&limit=20",
    "last": "/api/v1/users?page=8&limit=20"
  }
}

# Create new user
POST /api/v1/users HTTP/1.1
Content-Type: application/json

{
  "name": "Alice Johnson",
  "email": "alice@example.com",
  "role": "admin"
}

Response: 201 Created
Location: /api/v1/users/151
{
  "id": 151,
  "name": "Alice Johnson", 
  "email": "alice@example.com",
  "role": "admin",
  "created_at": "2025-06-12T10:30:00Z"
}
```

## Error Handling

### HTTP Error Response Format
```json
# Standard error response format
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid data",
    "details": [
      {
        "field": "email",
        "message": "Email format is invalid"
      },
      {
        "field": "age", 
        "message": "Age must be between 18 and 120"
      }
    ],
    "timestamp": "2025-06-12T10:30:00Z",
    "request_id": "req_abc123"
  }
}

# Different error scenarios:
400 Bad Request     - Client error (invalid syntax)
401 Unauthorized    - Authentication required
403 Forbidden       - Access denied
404 Not Found       - Resource doesn't exist
409 Conflict        - Resource conflict
422 Unprocessable   - Validation errors
429 Too Many Req    - Rate limit exceeded
500 Internal Error  - Server error
503 Service Unavail - Server overloaded
```

## Performance Optimization

### HTTP Performance Best Practices
```python
# Connection optimization
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

# Connection pooling and retry strategy
session = requests.Session()

# Configure retry strategy
retry_strategy = Retry(
    total=3,
    status_forcelist=[429, 500, 502, 503, 504],
    method_whitelist=["HEAD", "GET", "OPTIONS"],
    backoff_factor=1
)

# Configure connection pooling
adapter = HTTPAdapter(
    pool_connections=20,
    pool_maxsize=20,
    max_retries=retry_strategy
)

session.mount("http://", adapter)
session.mount("https://", adapter)

# Compression
headers = {
    'Accept-Encoding': 'gzip, deflate, br',
    'Accept': 'application/json',
    'User-Agent': 'MyApp/1.0'
}

# HTTP/2 with connection reuse
response = session.get('https://api.example.com/data', 
                      headers=headers, 
                      timeout=(3.05, 10))
```

### Performance Metrics
```
Key HTTP Performance Metrics:

Metric                 Target Value    Description
─────────────────────────────────────────────────────────────
DNS Lookup Time        <20ms          Domain name resolution
TCP Connection         <100ms         Initial connection setup
TLS Handshake          <200ms         SSL/TLS negotiation
Time to First Byte     <200ms         Server processing time
Content Download       <1s            Transfer complete
Total Page Load        <3s            Fully loaded page

Optimization Techniques:
1. HTTP/2 multiplexing
2. Compression (gzip, brotli)
3. CDN usage
4. Caching strategies
5. Minification
6. Image optimization
7. Connection keep-alive
8. Resource bundling
```

HTTP and HTTPS form the backbone of web communication, enabling everything from simple web pages to complex APIs and real-time applications. Understanding these protocols is essential for web development, API design, and network troubleshooting.
