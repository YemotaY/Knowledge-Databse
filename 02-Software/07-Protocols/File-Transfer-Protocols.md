# File Transfer Protocols

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

File transfer protocols enable the reliable exchange of files between systems across networks. From traditional FTP to modern secure alternatives, these protocols form the backbone of file sharing, backup systems, and content distribution.

## Overview

File transfer protocols have evolved from simple unencrypted transfers to secure, efficient, and feature-rich systems that support everything from basic file copying to complex synchronization and version control.

## FTP (File Transfer Protocol)

### FTP Architecture and Modes
```
FTP Control and Data Connections:

Active Mode (PORT):
Client                     Server
  │                          │
  │──── Control (Port 21) ───→│ Command channel
  │                          │
  │←─── Data (Port 20) ──────│ Server initiates data connection
  │                          │ to client's specified port

Passive Mode (PASV):
Client                     Server  
  │                          │
  │──── Control (Port 21) ───→│ Command channel
  │                          │
  │──── Data (Random) ──────→│ Client initiates data connection
  │                          │ to server's specified port

FTP Session Flow:
1. Client connects to server port 21 (control)
2. Authentication (USER/PASS)
3. Client requests data operation
4. Server opens data connection (port 20 or passive)
5. Data transfer occurs
6. Data connection closed
7. Control connection remains open
```

### FTP Commands and Responses
```
FTP Command Categories:

Access Control Commands:
USER username          Specify username
PASS password          Specify password
ACCT account          Specify account
CWD  pathname         Change working directory
CDUP                  Change to parent directory
SMNT pathname         Structure mount
REIN                  Reinitialize
QUIT                  Logout

Transfer Parameter Commands:
PORT host,port        Specify data port (active mode)
PASV                  Enter passive mode
TYPE A|I|E|L         Set transfer type (ASCII, Image, EBCDIC, Local)
STRU F|R|P           Set file structure (File, Record, Page)
MODE S|B|C           Set transfer mode (Stream, Block, Compressed)

Service Commands:
RETR filename         Retrieve file
STOR filename         Store file
STOU                  Store unique
APPE filename         Append to file
ALLO bytes           Allocate storage
REST marker          Restart transfer
RNFR filename        Rename from
RNTO filename        Rename to
ABOR                 Abort transfer
DELE filename        Delete file
RMD  pathname        Remove directory
MKD  pathname        Make directory
PWD                  Print working directory
LIST [pathname]      List files
NLST [pathname]      Name list
SITE command         Site-specific command
SYST                 System type
STAT [pathname]      Status
HELP [command]       Help
NOOP                 No operation

FTP Response Codes:
1xx  Preliminary positive (transfer starting)
2xx  Completion positive (action completed)
3xx  Intermediate positive (more info needed)
4xx  Transient negative (try again later)
5xx  Permanent negative (don't retry)

Common Response Examples:
150  File status okay; about to open data connection
200  Command okay
220  Service ready for new user
226  Closing data connection; file transfer successful
230  User logged in, proceed
250  Requested file action okay, completed
331  User name okay, need password
425  Can't open data connection
426  Connection closed; transfer aborted
450  Requested file action not taken
530  Not logged in
550  Requested action not taken; file unavailable
```

### FTP Session Example
```
FTP Session Transcript:

Client                              Server
  │                                   │
  │←────── 220 FTP server ready ──────│ Welcome message
  │                                   │
  │────── USER anonymous ────────────→│ Send username
  │                                   │
  │←────── 331 Password required ─────│ Request password
  │                                   │
  │────── PASS guest@example.com ────→│ Send password  
  │                                   │
  │←────── 230 Login successful ──────│ Authentication OK
  │                                   │
  │────── PWD ──────────────────────→│ Get current directory
  │                                   │
  │←────── 257 "/" is current dir ────│ Current directory
  │                                   │
  │────── TYPE I ───────────────────→│ Set binary mode
  │                                   │
  │←────── 200 Type set to I ─────────│ Binary mode confirmed
  │                                   │
  │────── PASV ─────────────────────→│ Enter passive mode
  │                                   │
  │←────── 227 Entering Passive ──────│ Passive mode info
  │        Mode (192,168,1,100,      │ IP: 192.168.1.100
  │        196,80)                    │ Port: 196*256+80=50256
  │                                   │
  │────── RETR largefile.zip ───────→│ Download file
  │                                   │
  │←────── 150 Opening data ──────────│ Starting transfer
  │        connection                 │
  │                                   │
  │ ═══ Data connection opened ═══════│
  │ ═══ File transfer in progress ════│
  │ ═══ Data connection closed ═══════│
  │                                   │
  │←────── 226 Transfer complete ─────│ Transfer successful
  │                                   │
  │────── QUIT ─────────────────────→│ End session
  │                                   │
  │←────── 221 Goodbye ───────────────│ Session ended
```

## FTPS (FTP Secure)

### FTPS vs SFTP
```
FTPS (FTP over SSL/TLS):
- Extension of FTP with SSL/TLS encryption
- Uses same command structure as FTP
- Two modes: Explicit (FTPES) and Implicit (FTPS)
- Separate encryption for control and data channels

SFTP (SSH File Transfer Protocol):
- Completely different protocol built on SSH
- Single encrypted connection for all operations
- More firewall-friendly (single port)
- Better security model

Comparison:
Feature              FTPS                SFTP
────────────────────────────────────────────────────
Base Protocol        FTP + SSL/TLS       SSH
Ports                21, 990 + data      22
Firewall Friendly    No (multiple ports) Yes (single port)
Authentication       Username/password   SSH keys + password
Encryption           Command + data      Everything
Complexity           Higher              Lower
Legacy Support       Better              Limited
```

### FTPS Connection Modes
```
Explicit FTPS (FTPES):
1. Client connects to port 21 (plain FTP)
2. Client sends AUTH TLS command
3. Server responds with 234 AUTH OK
4. TLS handshake occurs
5. Control connection now encrypted
6. Data connections use PROT P (protected)

Implicit FTPS:
1. Client connects to port 990
2. TLS handshake immediately
3. All communication encrypted from start
4. No plain text commands

FTPS Data Protection:
PROT C  Clear (no encryption)
PROT S  Safe (integrity only)
PROT E  Confidential (encryption only)  
PROT P  Private (integrity + encryption)

Example FTPS Session:
220 FTP Server ready
AUTH TLS
234 AUTH TLS OK. Starting TLS negotiation
[TLS Handshake]
USER username
331 Password required  
PASS password
230 Login successful
PBSZ 0
200 PBSZ set to 0
PROT P
200 Data connection protected
```

## SFTP (SSH File Transfer Protocol)

### SFTP Protocol Structure
```
SFTP over SSH:
┌─────────────────────────────────────────┐
│              SFTP Client                │
├─────────────────────────────────────────┤
│              SSH Protocol               │ ← Encryption, Authentication
├─────────────────────────────────────────┤
│                TCP                      │
├─────────────────────────────────────────┤
│                IP                       │
└─────────────────────────────────────────┘

SFTP Packet Format:
┌────────────┬─────────────┬──────────────┐
│   Length   │    Type     │     Data     │
│  (4 bytes) │  (1 byte)   │  (variable)  │
└────────────┴─────────────┴──────────────┘

SFTP Packet Types:
SSH_FXP_INIT        Initialize SFTP
SSH_FXP_VERSION     Protocol version  
SSH_FXP_OPEN        Open file/directory
SSH_FXP_CLOSE       Close file handle
SSH_FXP_READ        Read from file
SSH_FXP_WRITE       Write to file
SSH_FXP_LSTAT       Get file attributes
SSH_FXP_FSTAT       Get file handle attributes
SSH_FXP_SETSTAT     Set file attributes
SSH_FXP_FSETSTAT    Set file handle attributes
SSH_FXP_OPENDIR     Open directory
SSH_FXP_READDIR     Read directory entries
SSH_FXP_REMOVE      Remove file
SSH_FXP_MKDIR       Create directory
SSH_FXP_RMDIR       Remove directory
SSH_FXP_REALPATH    Get real path
SSH_FXP_STAT        Get path attributes
SSH_FXP_RENAME      Rename file/directory
SSH_FXP_READLINK    Read symbolic link
SSH_FXP_SYMLINK     Create symbolic link
```

### SFTP Authentication Methods
```python
import paramiko
import os

# Password Authentication
def sftp_password_auth(hostname, username, password):
    try:
        transport = paramiko.Transport((hostname, 22))
        transport.connect(username=username, password=password)
        sftp = paramiko.SFTPClient.from_transport(transport)
        return sftp
    except Exception as e:
        print(f"Authentication failed: {e}")
        return None

# Key-based Authentication
def sftp_key_auth(hostname, username, private_key_path):
    try:
        private_key = paramiko.RSAKey.from_private_key_file(private_key_path)
        transport = paramiko.Transport((hostname, 22))
        transport.connect(username=username, pkey=private_key)
        sftp = paramiko.SFTPClient.from_transport(transport)
        return sftp
    except Exception as e:
        print(f"Key authentication failed: {e}")
        return None

# Agent Authentication (SSH agent)
def sftp_agent_auth(hostname, username):
    try:
        agent = paramiko.Agent()
        agent_keys = agent.get_keys()
        
        for key in agent_keys:
            try:
                transport = paramiko.Transport((hostname, 22))
                transport.connect(username=username, pkey=key)
                sftp = paramiko.SFTPClient.from_transport(transport)
                return sftp
            except:
                continue
    except Exception as e:
        print(f"Agent authentication failed: {e}")
        return None

# SFTP Operations Example
def sftp_operations_example():
    sftp = sftp_key_auth('server.example.com', 'user', '/path/to/key')
    
    if sftp:
        # List directory contents
        files = sftp.listdir('/remote/path')
        print("Remote files:", files)
        
        # Upload file
        sftp.put('/local/file.txt', '/remote/file.txt')
        
        # Download file
        sftp.get('/remote/largefile.zip', '/local/largefile.zip')
        
        # Create directory
        sftp.mkdir('/remote/newdir')
        
        # Get file attributes
        attrs = sftp.stat('/remote/file.txt')
        print(f"File size: {attrs.st_size} bytes")
        print(f"Modified: {attrs.st_mtime}")
        
        # Change permissions
        sftp.chmod('/remote/file.txt', 0o644)
        
        sftp.close()
```

## SCP (Secure Copy Protocol)

### SCP Usage and Examples
```bash
# Basic SCP syntax
scp [options] source destination

# Copy file to remote server
scp localfile.txt user@server.com:/remote/path/

# Copy file from remote server  
scp user@server.com:/remote/file.txt /local/path/

# Copy directory recursively
scp -r /local/directory user@server.com:/remote/path/

# Copy with specific SSH key
scp -i ~/.ssh/private_key file.txt user@server.com:/path/

# Copy with custom SSH port
scp -P 2222 file.txt user@server.com:/path/

# Copy preserving timestamps and permissions
scp -p file.txt user@server.com:/path/

# Copy with compression
scp -C largefile.zip user@server.com:/path/

# Copy multiple files
scp file1.txt file2.txt user@server.com:/path/

# Copy through intermediate server (jump host)
scp -o ProxyJump=jump.server.com file.txt user@target.com:/path/

# Show progress during transfer
scp -v file.txt user@server.com:/path/

# Limit bandwidth (in Kbit/s)
scp -l 1000 largefile.zip user@server.com:/path/

SCP Options:
-r  Recursive (for directories)
-p  Preserve timestamps and permissions
-v  Verbose output
-C  Enable compression
-i  Specify identity file (private key)
-P  Specify port number
-l  Limit bandwidth
-o  SSH options
-q  Quiet mode (suppress output)
-4  Force IPv4
-6  Force IPv6
```

## rsync (Remote Synchronization)

### rsync Features and Usage
```bash
# Basic rsync syntax
rsync [options] source destination

# Local synchronization
rsync -av /source/directory/ /destination/directory/

# Remote synchronization (push)
rsync -av /local/directory/ user@server.com:/remote/directory/

# Remote synchronization (pull)
rsync -av user@server.com:/remote/directory/ /local/directory/

# rsync over SSH with custom port
rsync -av -e "ssh -p 2222" /local/ user@server.com:/remote/

# Incremental backup with hard links
rsync -av --link-dest=/backup/2025-06-11 /source/ /backup/2025-06-12/

# Exclude files and directories
rsync -av --exclude='*.log' --exclude='temp/' /source/ /dest/

# Delete files in destination not in source
rsync -av --delete /source/ /dest/

# Dry run (show what would be transferred)
rsync -av --dry-run /source/ /dest/

# Show progress during transfer
rsync -av --progress /large/files/ user@server.com:/backup/

# Bandwidth limiting
rsync -av --bwlimit=1000 /source/ user@server.com:/dest/

# Resume interrupted transfers
rsync -av --partial --progress /source/ user@server.com:/dest/

rsync Key Options:
-a  Archive mode (recursive, preserve permissions, timestamps, etc.)
-v  Verbose output
-z  Compress data during transfer
-P  Show progress and keep partial files
-u  Update only (skip newer files on destination)
-n  Dry run (don't actually transfer)
-e  Specify remote shell command
--delete        Delete files in destination not in source
--exclude       Exclude files/directories matching pattern
--include       Include files/directories matching pattern
--link-dest     Hard link to files in another directory
--bwlimit       Limit bandwidth usage
--partial       Keep partially transferred files
--sparse        Handle sparse files efficiently
--inplace       Update files in place (don't create temp files)
```

### rsync Advanced Examples
```python
import subprocess
import os
from datetime import datetime

class RsyncManager:
    def __init__(self, ssh_key=None, ssh_port=22):
        self.ssh_key = ssh_key
        self.ssh_port = ssh_port
    
    def build_ssh_command(self):
        """Build SSH command with custom options"""
        ssh_cmd = ["ssh"]
        if self.ssh_port != 22:
            ssh_cmd.extend(["-p", str(self.ssh_port)])
        if self.ssh_key:
            ssh_cmd.extend(["-i", self.ssh_key])
        return " ".join(ssh_cmd)
    
    def sync_directory(self, source, destination, options=None):
        """Synchronize directories with rsync"""
        if options is None:
            options = ["-av", "--progress"]
        
        cmd = ["rsync"] + options
        
        if "@" in destination:  # Remote destination
            cmd.extend(["-e", self.build_ssh_command()])
        
        cmd.extend([source, destination])
        
        try:
            result = subprocess.run(cmd, capture_output=True, text=True)
            return result.returncode == 0, result.stdout, result.stderr
        except Exception as e:
            return False, "", str(e)
    
    def incremental_backup(self, source, backup_root, host=None):
        """Create incremental backup using hard links"""
        timestamp = datetime.now().strftime("%Y-%m-%d_%H-%M-%S")
        
        if host:
            backup_dir = f"{host}:{backup_root}/{timestamp}"
            latest_link = f"{host}:{backup_root}/latest"
        else:
            backup_dir = f"{backup_root}/{timestamp}"
            latest_link = f"{backup_root}/latest"
        
        options = [
            "-av",
            "--progress", 
            "--delete",
            f"--link-dest={latest_link}"
        ]
        
        success, stdout, stderr = self.sync_directory(source, backup_dir, options)
        
        if success and not host:  # Update latest symlink for local backups
            if os.path.exists(f"{backup_root}/latest"):
                os.remove(f"{backup_root}/latest")
            os.symlink(timestamp, f"{backup_root}/latest")
        
        return success, stdout, stderr

# Usage example
rsync_mgr = RsyncManager(ssh_key="/home/user/.ssh/backup_key", ssh_port=2222)

# Simple directory sync
success, output, error = rsync_mgr.sync_directory(
    "/home/user/documents/",
    "backup@server.com:/backups/documents/"
)

# Incremental backup
success, output, error = rsync_mgr.incremental_backup(
    "/home/user/",
    "/backups/user_backup",
    "backup@server.com"
)
```

## Modern File Transfer Solutions

### WebDAV (Web Distributed Authoring and Versioning)
```
WebDAV HTTP Extensions:

Standard HTTP Methods:
GET, POST, HEAD, PUT, DELETE, OPTIONS, TRACE

WebDAV-specific Methods:
PROPFIND    Retrieve properties of resources
PROPPATCH   Change properties of resources
MKCOL       Create collections (directories)
COPY        Copy resources
MOVE        Move resources
LOCK        Lock resources for editing
UNLOCK      Release locks

WebDAV Properties:
creationdate         When resource was created
displayname          Human-readable name
getcontentlanguage   Content language
getcontentlength     Content length in bytes
getcontenttype       MIME type
getetag              Entity tag
getlastmodified      Last modification date
lockdiscovery        Active locks
resourcetype         Resource type (collection/file)
source               Source of resource
supportedlock        Supported lock types

WebDAV Example Request:
PROPFIND /documents/ HTTP/1.1
Host: webdav.example.com
Depth: 1
Content-Type: application/xml
Content-Length: 183

<?xml version="1.0" encoding="utf-8" ?>
<D:propfind xmlns:D="DAV:">
  <D:prop>
    <D:displayname/>
    <D:getcontentlength/>
    <D:getlastmodified/>
    <D:resourcetype/>
  </D:prop>
</D:propfind>

WebDAV Clients:
- Windows: Built-in WebDAV redirector
- macOS: Finder Connect to Server
- Linux: davfs2, cadaver
- Web: Browser-based clients
- Mobile: Apps supporting WebDAV
```

### Modern Alternatives and APIs
```python
# Cloud Storage APIs Example

# AWS S3 Transfer
import boto3
from botocore.exceptions import ClientError

class S3FileTransfer:
    def __init__(self, aws_access_key, aws_secret_key, region='us-east-1'):
        self.s3_client = boto3.client(
            's3',
            aws_access_key_id=aws_access_key,
            aws_secret_access_key=aws_secret_key,
            region_name=region
        )
    
    def upload_file(self, local_file, bucket, s3_key):
        """Upload file to S3 with progress tracking"""
        try:
            self.s3_client.upload_file(
                local_file, bucket, s3_key,
                Callback=self._progress_callback
            )
            return True
        except ClientError as e:
            print(f"Upload failed: {e}")
            return False
    
    def download_file(self, bucket, s3_key, local_file):
        """Download file from S3"""
        try:
            self.s3_client.download_file(bucket, s3_key, local_file)
            return True
        except ClientError as e:
            print(f"Download failed: {e}")
            return False
    
    def _progress_callback(self, bytes_transferred):
        """Callback for upload progress"""
        print(f"Transferred: {bytes_transferred} bytes")

# HTTP-based File Transfer
import requests
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

class HTTPFileTransfer:
    def __init__(self):
        self.session = requests.Session()
        
        # Configure retry strategy
        retry_strategy = Retry(
            total=3,
            status_forcelist=[429, 500, 502, 503, 504],
            method_whitelist=["HEAD", "GET", "PUT", "DELETE", "OPTIONS", "TRACE"]
        )
        
        adapter = HTTPAdapter(max_retries=retry_strategy)
        self.session.mount("http://", adapter)
        self.session.mount("https://", adapter)
    
    def upload_chunked(self, file_path, upload_url, chunk_size=8192):
        """Upload large file in chunks"""
        file_size = os.path.getsize(file_path)
        
        with open(file_path, 'rb') as f:
            uploaded = 0
            
            while uploaded < file_size:
                chunk = f.read(chunk_size)
                if not chunk:
                    break
                
                headers = {
                    'Content-Range': f'bytes {uploaded}-{uploaded + len(chunk) - 1}/{file_size}',
                    'Content-Length': str(len(chunk))
                }
                
                response = self.session.put(
                    upload_url,
                    data=chunk,
                    headers=headers
                )
                
                if response.status_code not in [200, 201, 206]:
                    return False
                
                uploaded += len(chunk)
                print(f"Uploaded: {uploaded}/{file_size} bytes ({uploaded/file_size*100:.1f}%)")
        
        return True
    
    def download_resumable(self, download_url, local_file):
        """Download with resume capability"""
        headers = {}
        initial_pos = 0
        
        # Check if partial file exists
        if os.path.exists(local_file):
            initial_pos = os.path.getsize(local_file)
            headers['Range'] = f'bytes={initial_pos}-'
        
        response = self.session.get(download_url, headers=headers, stream=True)
        
        mode = 'ab' if initial_pos > 0 else 'wb'
        
        with open(local_file, mode) as f:
            for chunk in response.iter_content(chunk_size=8192):
                if chunk:
                    f.write(chunk)
        
        return response.status_code in [200, 206]

# P2P File Transfer (BitTorrent-like)
class P2PFileTransfer:
    def __init__(self):
        self.peers = set()
        self.file_chunks = {}
    
    def add_peer(self, peer_address):
        """Add peer to the network"""
        self.peers.add(peer_address)
    
    def request_file(self, file_hash, chunk_size=1024*1024):
        """Request file from peers using chunk-based transfer"""
        # Implementation would involve:
        # 1. Query peers for file availability
        # 2. Download chunks from multiple peers
        # 3. Verify chunk integrity
        # 4. Reassemble file
        pass
    
    def serve_file(self, file_path):
        """Make file available to peers"""
        # Implementation would involve:
        # 1. Split file into chunks
        # 2. Calculate chunk hashes
        # 3. Announce availability to peers
        # 4. Serve chunks on request
        pass
```

## Performance Optimization

### File Transfer Performance Tuning
```python
import threading
import queue
import hashlib
import time

class OptimizedFileTransfer:
    def __init__(self, num_threads=4, chunk_size=1024*1024):
        self.num_threads = num_threads
        self.chunk_size = chunk_size
        self.transfer_queue = queue.Queue()
    
    def parallel_upload(self, files, upload_function):
        """Upload multiple files in parallel"""
        
        def worker():
            while True:
                try:
                    file_path = self.transfer_queue.get_nowait()
                    upload_function(file_path)
                    self.transfer_queue.task_done()
                except queue.Empty:
                    break
        
        # Add files to queue
        for file_path in files:
            self.transfer_queue.put(file_path)
        
        # Start worker threads
        threads = []
        for _ in range(self.num_threads):
            t = threading.Thread(target=worker)
            t.start()
            threads.append(t)
        
        # Wait for completion
        for t in threads:
            t.join()
    
    def verify_transfer(self, local_file, remote_file_hash):
        """Verify file transfer integrity"""
        sha256_hash = hashlib.sha256()
        
        with open(local_file, "rb") as f:
            for chunk in iter(lambda: f.read(self.chunk_size), b""):
                sha256_hash.update(chunk)
        
        return sha256_hash.hexdigest() == remote_file_hash
    
    def measure_throughput(self, file_size, transfer_time):
        """Calculate transfer throughput"""
        throughput_mbps = (file_size * 8) / (transfer_time * 1000000)
        return throughput_mbps

# Performance monitoring
class TransferMonitor:
    def __init__(self):
        self.start_time = None
        self.bytes_transferred = 0
        self.last_update = None
        self.speed_samples = []
    
    def start_transfer(self):
        self.start_time = time.time()
        self.last_update = self.start_time
    
    def update_progress(self, bytes_transferred):
        current_time = time.time()
        
        if self.last_update:
            time_delta = current_time - self.last_update
            bytes_delta = bytes_transferred - self.bytes_transferred
            
            if time_delta > 0:
                speed = bytes_delta / time_delta
                self.speed_samples.append(speed)
                
                # Keep only recent samples
                if len(self.speed_samples) > 10:
                    self.speed_samples.pop(0)
        
        self.bytes_transferred = bytes_transferred
        self.last_update = current_time
    
    def get_average_speed(self):
        if self.speed_samples:
            return sum(self.speed_samples) / len(self.speed_samples)
        return 0
    
    def get_eta(self, total_size):
        avg_speed = self.get_average_speed()
        if avg_speed > 0:
            remaining_bytes = total_size - self.bytes_transferred
            return remaining_bytes / avg_speed
        return float('inf')

# Usage example
monitor = TransferMonitor()
monitor.start_transfer()

# During file transfer loop:
# monitor.update_progress(current_bytes_transferred)
# print(f"Speed: {monitor.get_average_speed()/1024/1024:.2f} MB/s")
# print(f"ETA: {monitor.get_eta(total_file_size):.0f} seconds")
```

File transfer protocols have evolved from simple unencrypted FTP to sophisticated, secure, and efficient systems. Modern applications often combine multiple protocols and techniques to achieve optimal performance, security, and reliability for different use cases.
