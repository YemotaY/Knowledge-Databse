# Real-Time Protocols (WebRTC, RTP, RTSP)

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

Real-time protocols enable live communication and streaming applications, supporting voice calls, video conferencing, live streaming, and interactive multimedia applications with minimal latency.

## Overview

Real-time protocols are designed for applications that require immediate delivery of audio, video, and data with strict timing constraints. They prioritize low latency over perfect reliability, often using UDP for transport and implementing specialized mechanisms for quality of service.

## RTP (Real-time Transport Protocol)

### RTP Architecture and Components
```
RTP Stack Architecture:

Application (Video/Audio Codec)
         │
    ┌────┴────┐
    │   RTP   │  ← Media delivery
    └────┬────┘
    ┌────┴────┐
    │  RTCP   │  ← Control and feedback
    └────┬────┘
    ┌────┴────┐
    │   UDP   │  ← Transport layer
    └────┬────┘
    ┌────┴────┐
    │   IP    │  ← Network layer
    └─────────┘

RTP Components:
- RTP: Carries media data (audio/video)
- RTCP: Control protocol for QoS feedback
- SRTP: Secure RTP with encryption
- RTCP: Real-time Control Protocol

Port Usage:
- RTP: Even port numbers (e.g., 5004, 5006, 5008)
- RTCP: Odd port numbers (e.g., 5005, 5007, 5009)
```

### RTP Packet Format
```
RTP Header Structure:
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│V=2│P│X│  CC   │M│     PT      │       Sequence Number         │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                           Timestamp                           │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                      SSRC of source                          │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                    CSRC list (0-15 items)                     │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                      Extension (optional)                     │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                           Payload                             │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘

Header Fields:
V (Version):       RTP version (always 2)
P (Padding):       Padding flag
X (Extension):     Extension header present
CC (CSRC Count):   Number of CSRC identifiers (0-15)
M (Marker):        Application-specific marker
PT (Payload Type): Identifies payload format
Sequence Number:   Incrementing packet number
Timestamp:         Sampling instant of first byte
SSRC:             Synchronization source identifier
CSRC:             Contributing source identifiers

Common Payload Types:
PT    Encoding         Sampling Rate    Channels
──────────────────────────────────────────────────
0     PCMU (G.711μ)    8000 Hz         1
3     GSM              8000 Hz         1
4     G.723            8000 Hz         1
8     PCMA (G.711A)    8000 Hz         1
9     G.722            8000 Hz         1
14    MPA              90000 Hz        -
18    G.729            8000 Hz         1
26    JPEG             90000 Hz        -
31    H.261            90000 Hz        -
32    MPV              90000 Hz        -
96-127 Dynamic         Variable        Variable
```

### RTCP (RTP Control Protocol)
```
RTCP Packet Types:

SR (Sender Report):
- Sent by active senders
- Contains transmission statistics
- Timestamp correlation info

RR (Receiver Report):
- Sent by receivers  
- Reception quality feedback
- Loss statistics

SDES (Source Description):
- Source identification
- CNAME (canonical name)
- Additional metadata

BYE (Goodbye):
- Participant leaving session
- Reason for departure

APP (Application-specific):
- Custom application data
- Experimental use

RTCP Timing:
- Bandwidth limited (5% of session)
- Randomized intervals
- Scales with number of participants

Example RTCP Sender Report:
Packet Type: SR (200)
Length: 28 bytes
SSRC: 0x12345678
NTP Timestamp: 0x83AA7E80 00000000
RTP Timestamp: 160000
Packet Count: 47
Octet Count: 8640

Reception Report Block:
SSRC: 0x87654321  
Fraction Lost: 0 (0%)
Cumulative Lost: 0
Highest Seq: 46
Jitter: 320
Last SR: 0x83AA7E80
Delay since Last SR: 65536 (1 second)
```

## RTSP (Real Time Streaming Protocol)

### RTSP Protocol Overview
```
RTSP Architecture:

Media Server                    Client
     │                           │
     │←───── DESCRIBE ───────────│  Get media description
     │                           │
     │────── 200 OK + SDP ──────→│  Session description
     │                           │
     │←───── SETUP ──────────────│  Setup transport
     │                           │
     │────── 200 OK ────────────→│  Transport confirmed
     │                           │
     │←───── PLAY ───────────────│  Start streaming
     │                           │
     │────── 200 OK ────────────→│  Stream started
     │                           │
     │ ═════ RTP/UDP ═══════════→│  Media stream
     │                           │
     │←───── PAUSE ──────────────│  Pause stream
     │                           │
     │────── 200 OK ────────────→│  Stream paused
     │                           │
     │←───── TEARDOWN ───────────│  End session
     │                           │
     │────── 200 OK ────────────→│  Session ended

RTSP vs HTTP:
- Similar syntax but different semantics
- Stateful protocol (maintains session)
- Bi-directional communication
- Real-time control operations
```

### RTSP Methods and Messages
```
RTSP Methods:

OPTIONS        Query supported methods
DESCRIBE       Get media description (SDP)
ANNOUNCE       Update session description
SETUP          Specify transport mechanism
PLAY           Start media delivery
PAUSE          Pause media delivery
TEARDOWN       Stop media delivery and free resources
GET_PARAMETER  Retrieve parameter value
SET_PARAMETER  Set parameter value
REDIRECT       Redirect client to another server
RECORD         Start recording

RTSP Session Example:

1. Client requests media description:
DESCRIBE rtsp://server.com/movie.mp4 RTSP/1.0
CSeq: 1
User-Agent: VLC Media Player

2. Server responds with SDP:
RTSP/1.0 200 OK
CSeq: 1
Content-Type: application/sdp
Content-Length: 460

v=0
o=- 1234567890 1234567890 IN IP4 192.168.1.100
s=Movie Stream
c=IN IP4 0.0.0.0
t=0 0
m=video 0 RTP/AVP 96
a=rtpmap:96 H264/90000
a=control:trackID=1
m=audio 0 RTP/AVP 97
a=rtpmap:97 MP4A-LATM/44100
a=control:trackID=2

3. Client sets up video transport:
SETUP rtsp://server.com/movie.mp4/trackID=1 RTSP/1.0
CSeq: 2
Transport: RTP/AVP;unicast;client_port=5004-5005

4. Server confirms transport:
RTSP/1.0 200 OK
CSeq: 2
Session: 12345678
Transport: RTP/AVP;unicast;client_port=5004-5005;server_port=6970-6971

5. Client starts playback:
PLAY rtsp://server.com/movie.mp4 RTSP/1.0
CSeq: 3
Session: 12345678
Range: npt=0-

6. Server starts streaming:
RTSP/1.0 200 OK
CSeq: 3
Session: 12345678
Range: npt=0-125.50
RTP-Info: url=trackID=1;seq=12345;rtptime=1000000
```

### SDP (Session Description Protocol)
```
SDP Structure for RTSP:

v=0                                    # Version
o=- 1234567890 1234567890 IN IP4 server.com  # Origin
s=Live Stream                          # Session name
i=High quality live video              # Information
c=IN IP4 224.1.1.1                    # Connection info
t=0 0                                  # Time (0 0 = unbounded)
a=tool:RTSP Server 1.0                 # Attributes

m=video 5004 RTP/AVP 96               # Media description
c=IN IP4 224.1.1.1/255               # Connection (multicast)
b=AS:1000                             # Bandwidth (kbps)
a=rtpmap:96 H264/90000                # Payload mapping
a=fmtp:96 profile-level-id=42e01e     # Format parameters
a=control:trackID=video               # Control URL

m=audio 5006 RTP/AVP 97               # Audio media
a=rtpmap:97 MP4A-LATM/44100/2         # Audio payload
a=fmtp:97 cpresent=0;config=400023203fc0  # Audio config
a=control:trackID=audio               # Audio control

SDP Media Types:
video    Video stream
audio    Audio stream  
text     Text/subtitle stream
application  Data stream
message  Message stream

SDP Attributes:
rtpmap       RTP payload type mapping
fmtp         Format-specific parameters
control      Media control URL
orient       Video orientation
framerate    Video frame rate
quality      Quality hint
sendrecv     Send and receive
sendonly     Send only
recvonly     Receive only
inactive     No media transfer
```

## WebRTC (Web Real-Time Communication)

### WebRTC Architecture
```
WebRTC Components:

┌─────────────────────────────────────────────────────────────┐
│                   Web Application                          │
├─────────────────────────────────────────────────────────────┤
│              WebRTC JavaScript APIs                        │
├─────────────────────────────────────────────────────────────┤
│    getUserMedia  │  RTCPeerConnection  │  RTCDataChannel   │
├─────────────────────────────────────────────────────────────┤
│  Audio/Video     │    Session Mgmt     │   Data Transfer   │
│  Capture         │    ICE/STUN/TURN    │   P2P Messaging   │
├─────────────────────────────────────────────────────────────┤
│              Browser WebRTC Engine                         │
├─────────────────────────────────────────────────────────────┤
│  Audio Engine │ Video Engine │ Network Engine │ Security   │
├─────────────────────────────────────────────────────────────┤
│              Operating System                               │
└─────────────────────────────────────────────────────────────┘

WebRTC Protocol Stack:
┌─────────────────┐
│  Application    │  ← JavaScript APIs
├─────────────────┤
│  SRTP/SCTP      │  ← Secure media/data
├─────────────────┤  
│  DTLS           │  ← Security handshake
├─────────────────┤
│  ICE/STUN/TURN  │  ← NAT traversal
├─────────────────┤
│  UDP/TCP        │  ← Transport
├─────────────────┤
│  IP             │  ← Network
└─────────────────┘
```

### WebRTC Signaling and Connection Setup
```javascript
// WebRTC Connection Setup Example

class WebRTCManager {
    constructor() {
        this.localConnection = null;
        this.remoteConnection = null;
        this.signalingSocket = null;
        this.localStream = null;
    }

    async initializeConnection() {
        // Get user media (camera/microphone)
        try {
            this.localStream = await navigator.mediaDevices.getUserMedia({
                video: { 
                    width: 1280, 
                    height: 720,
                    frameRate: 30
                },
                audio: {
                    echoCancellation: true,
                    noiseSuppression: true,
                    autoGainControl: true
                }
            });
        } catch (error) {
            console.error('Failed to get user media:', error);
            return;
        }

        // Create peer connection
        this.localConnection = new RTCPeerConnection({
            iceServers: [
                { urls: 'stun:stun.l.google.com:19302' },
                { urls: 'stun:stun1.l.google.com:19302' },
                {
                    urls: 'turn:turn.example.com:3478',
                    username: 'user',
                    credential: 'password'
                }
            ],
            iceCandidatePoolSize: 10
        });

        // Add local stream to connection
        this.localStream.getTracks().forEach(track => {
            this.localConnection.addTrack(track, this.localStream);
        });

        // Handle ICE candidates
        this.localConnection.onicecandidate = (event) => {
            if (event.candidate) {
                this.sendSignalingMessage({
                    type: 'ice-candidate',
                    candidate: event.candidate
                });
            }
        };

        // Handle remote stream
        this.localConnection.ontrack = (event) => {
            const remoteVideo = document.getElementById('remoteVideo');
            remoteVideo.srcObject = event.streams[0];
        };

        // Handle connection state changes
        this.localConnection.onconnectionstatechange = () => {
            console.log('Connection state:', this.localConnection.connectionState);
        };
    }

    async createOffer() {
        // Create and set local offer
        const offer = await this.localConnection.createOffer({
            offerToReceiveAudio: true,
            offerToReceiveVideo: true
        });
        
        await this.localConnection.setLocalDescription(offer);
        
        // Send offer through signaling channel
        this.sendSignalingMessage({
            type: 'offer',
            offer: offer
        });
    }

    async handleOffer(offer) {
        // Set remote offer
        await this.localConnection.setRemoteDescription(offer);
        
        // Create and send answer
        const answer = await this.localConnection.createAnswer();
        await this.localConnection.setLocalDescription(answer);
        
        this.sendSignalingMessage({
            type: 'answer',
            answer: answer
        });
    }

    async handleAnswer(answer) {
        // Set remote answer
        await this.localConnection.setRemoteDescription(answer);
    }

    async handleIceCandidate(candidate) {
        // Add ICE candidate
        await this.localConnection.addIceCandidate(candidate);
    }

    sendSignalingMessage(message) {
        // Send through WebSocket or other signaling mechanism
        if (this.signalingSocket && this.signalingSocket.readyState === WebSocket.OPEN) {
            this.signalingSocket.send(JSON.stringify(message));
        }
    }

    setupSignaling() {
        this.signalingSocket = new WebSocket('wss://signaling.example.com');
        
        this.signalingSocket.onmessage = async (event) => {
            const message = JSON.parse(event.data);
            
            switch (message.type) {
                case 'offer':
                    await this.handleOffer(message.offer);
                    break;
                case 'answer':
                    await this.handleAnswer(message.answer);
                    break;
                case 'ice-candidate':
                    await this.handleIceCandidate(message.candidate);
                    break;
            }
        };
    }
}

// Usage
const webrtc = new WebRTCManager();
webrtc.setupSignaling();
webrtc.initializeConnection();
```

### WebRTC Data Channels
```javascript
// WebRTC Data Channel Example

class WebRTCDataChannel {
    constructor(peerConnection) {
        this.peerConnection = peerConnection;
        this.dataChannel = null;
        this.messageQueue = [];
    }

    createDataChannel(channelName = 'dataChannel') {
        // Create data channel
        this.dataChannel = this.peerConnection.createDataChannel(channelName, {
            ordered: true,              // Guarantee order
            maxRetransmits: 3,         // Retry failed transmissions
            protocol: 'json',          // Application protocol
            negotiated: false,         // Use in-band negotiation
            id: null                   // Auto-assign ID
        });

        this.setupDataChannelHandlers();
    }

    setupDataChannelHandlers() {
        this.dataChannel.onopen = () => {
            console.log('Data channel opened');
            this.sendQueuedMessages();
        };

        this.dataChannel.onclose = () => {
            console.log('Data channel closed');
        };

        this.dataChannel.onerror = (error) => {
            console.error('Data channel error:', error);
        };

        this.dataChannel.onmessage = (event) => {
            this.handleMessage(event.data);
        };
    }

    handleIncomingDataChannel(event) {
        // Handle data channel from remote peer
        this.dataChannel = event.channel;
        this.setupDataChannelHandlers();
    }

    sendMessage(message) {
        const data = JSON.stringify({
            timestamp: Date.now(),
            type: 'message',
            payload: message
        });

        if (this.dataChannel && this.dataChannel.readyState === 'open') {
            this.dataChannel.send(data);
        } else {
            // Queue message if channel not ready
            this.messageQueue.push(data);
        }
    }

    sendFile(file) {
        const chunkSize = 16384; // 16KB chunks
        let offset = 0;

        const sendNextChunk = () => {
            if (offset >= file.size) {
                this.sendMessage({
                    type: 'file-complete',
                    filename: file.name,
                    size: file.size
                });
                return;
            }

            const chunk = file.slice(offset, offset + chunkSize);
            const reader = new FileReader();

            reader.onload = (event) => {
                this.dataChannel.send(event.target.result);
                offset += chunkSize;
                
                // Send next chunk
                setTimeout(sendNextChunk, 10); // Small delay to prevent overwhelming
            };

            reader.readAsArrayBuffer(chunk);
        };

        // Send file metadata first
        this.sendMessage({
            type: 'file-start',
            filename: file.name,
            size: file.size,
            type: file.type
        });

        sendNextChunk();
    }

    sendQueuedMessages() {
        while (this.messageQueue.length > 0) {
            const message = this.messageQueue.shift();
            this.dataChannel.send(message);
        }
    }

    handleMessage(data) {
        try {
            const message = JSON.parse(data);
            
            switch (message.type) {
                case 'message':
                    console.log('Received message:', message.payload);
                    break;
                case 'file-start':
                    this.handleFileStart(message);
                    break;
                case 'file-complete':
                    this.handleFileComplete(message);
                    break;
                default:
                    console.log('Unknown message type:', message.type);
            }
        } catch (error) {
            // Handle binary data (file chunks)
            this.handleFileChunk(data);
        }
    }

    handleFileStart(metadata) {
        this.currentFile = {
            name: metadata.filename,
            size: metadata.size,
            type: metadata.type,
            chunks: [],
            receivedSize: 0
        };
    }

    handleFileChunk(chunk) {
        if (this.currentFile) {
            this.currentFile.chunks.push(chunk);
            this.currentFile.receivedSize += chunk.byteLength;
            
            // Update progress
            const progress = (this.currentFile.receivedSize / this.currentFile.size) * 100;
            console.log(`File transfer progress: ${progress.toFixed(1)}%`);
        }
    }

    handleFileComplete(metadata) {
        if (this.currentFile) {
            // Combine chunks into complete file
            const blob = new Blob(this.currentFile.chunks, { type: this.currentFile.type });
            
            // Create download link
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.href = url;
            link.download = this.currentFile.name;
            link.click();
            
            URL.revokeObjectURL(url);
            this.currentFile = null;
        }
    }
}

// Setup data channel on peer connection
peerConnection.ondatachannel = (event) => {
    const dataChannelManager = new WebRTCDataChannel(peerConnection);
    dataChannelManager.handleIncomingDataChannel(event);
};
```

## Quality of Service and Optimization

### Real-Time Protocol QoS
```python
import socket
import struct
import time
import threading
from collections import deque

class RTPQoSMonitor:
    def __init__(self):
        self.packet_loss_rate = 0.0
        self.average_jitter = 0.0
        self.round_trip_time = 0.0
        self.packets_received = 0
        self.packets_lost = 0
        self.last_sequence_number = 0
        self.jitter_buffer = deque(maxlen=100)
        self.arrival_times = deque(maxlen=100)
    
    def process_rtp_packet(self, packet_data):
        """Process incoming RTP packet for QoS metrics"""
        # Parse RTP header
        if len(packet_data) < 12:
            return
        
        header = struct.unpack('!BBHII', packet_data[:12])
        sequence_number = header[2]
        timestamp = header[3]
        
        current_time = time.time()
        
        # Calculate packet loss
        if self.packets_received > 0:
            expected_sequence = (self.last_sequence_number + 1) % 65536
            if sequence_number != expected_sequence:
                # Packets lost
                if sequence_number > expected_sequence:
                    lost = sequence_number - expected_sequence
                else:
                    lost = (65536 - expected_sequence) + sequence_number
                self.packets_lost += lost
        
        self.packets_received += 1
        self.last_sequence_number = sequence_number
        
        # Calculate packet loss rate
        total_packets = self.packets_received + self.packets_lost
        if total_packets > 0:
            self.packet_loss_rate = self.packets_lost / total_packets
        
        # Calculate jitter
        self.arrival_times.append(current_time)
        if len(self.arrival_times) >= 2:
            arrival_interval = self.arrival_times[-1] - self.arrival_times[-2]
            self.jitter_buffer.append(arrival_interval)
            
            if len(self.jitter_buffer) > 1:
                mean_interval = sum(self.jitter_buffer) / len(self.jitter_buffer)
                variance = sum((x - mean_interval) ** 2 for x in self.jitter_buffer) / len(self.jitter_buffer)
                self.average_jitter = variance ** 0.5
    
    def get_qos_metrics(self):
        """Get current QoS metrics"""
        return {
            'packet_loss_rate': self.packet_loss_rate,
            'jitter': self.average_jitter,
            'rtt': self.round_trip_time,
            'packets_received': self.packets_received,
            'packets_lost': self.packets_lost
        }

class AdaptiveStreamingManager:
    def __init__(self):
        self.quality_levels = [
            {'width': 320, 'height': 240, 'bitrate': 300, 'fps': 15},    # Low
            {'width': 640, 'height': 480, 'bitrate': 800, 'fps': 24},   # Medium
            {'width': 1280, 'height': 720, 'bitrate': 2000, 'fps': 30}, # High
            {'width': 1920, 'height': 1080, 'bitrate': 4000, 'fps': 30} # Ultra
        ]
        self.current_quality = 1  # Start with medium quality
        self.qos_monitor = RTPQoSMonitor()
    
    def adjust_quality(self):
        """Adjust streaming quality based on network conditions"""
        metrics = self.qos_monitor.get_qos_metrics()
        
        # Determine quality adjustment
        if metrics['packet_loss_rate'] > 0.05 or metrics['jitter'] > 0.1:
            # Poor network conditions - reduce quality
            if self.current_quality > 0:
                self.current_quality -= 1
                print(f"Reducing quality to level {self.current_quality}")
        elif metrics['packet_loss_rate'] < 0.01 and metrics['jitter'] < 0.05:
            # Good network conditions - increase quality
            if self.current_quality < len(self.quality_levels) - 1:
                self.current_quality += 1
                print(f"Increasing quality to level {self.current_quality}")
        
        return self.quality_levels[self.current_quality]
    
    def start_monitoring(self):
        """Start continuous QoS monitoring"""
        def monitor_loop():
            while True:
                time.sleep(5)  # Check every 5 seconds
                self.adjust_quality()
        
        monitor_thread = threading.Thread(target=monitor_loop, daemon=True)
        monitor_thread.start()

# Example usage
qos_monitor = RTPQoSMonitor()
streaming_manager = AdaptiveStreamingManager()
streaming_manager.start_monitoring()

# In your RTP packet processing loop:
# qos_monitor.process_rtp_packet(packet_data)
```

### Real-Time Performance Optimization
```javascript
// WebRTC Performance Optimization

class WebRTCOptimizer {
    constructor(peerConnection) {
        this.peerConnection = peerConnection;
        this.stats = {
            video: {},
            audio: {},
            connection: {}
        };
    }

    async optimizeConnection() {
        // Get connection stats
        const stats = await this.peerConnection.getStats();
        this.processStats(stats);
        
        // Apply optimizations based on current performance
        this.adjustVideoQuality();
        this.optimizeAudioSettings();
        this.optimizeNetworkSettings();
    }

    processStats(stats) {
        stats.forEach(report => {
            switch (report.type) {
                case 'inbound-rtp':
                    if (report.mediaType === 'video') {
                        this.stats.video = {
                            packetsReceived: report.packetsReceived,
                            packetsLost: report.packetsLost,
                            bytesReceived: report.bytesReceived,
                            framesReceived: report.framesReceived,
                            framesDropped: report.framesDropped,
                            jitter: report.jitter
                        };
                    } else if (report.mediaType === 'audio') {
                        this.stats.audio = {
                            packetsReceived: report.packetsReceived,
                            packetsLost: report.packetsLost,
                            jitter: report.jitter
                        };
                    }
                    break;
                
                case 'candidate-pair':
                    if (report.state === 'succeeded') {
                        this.stats.connection = {
                            currentRoundTripTime: report.currentRoundTripTime,
                            availableOutgoingBitrate: report.availableOutgoingBitrate,
                            bytesReceived: report.bytesReceived,
                            bytesSent: report.bytesSent
                        };
                    }
                    break;
            }
        });
    }

    adjustVideoQuality() {
        const video = this.stats.video;
        const connection = this.stats.connection;
        
        // Calculate packet loss rate
        const totalPackets = video.packetsReceived + video.packetsLost;
        const lossRate = totalPackets > 0 ? video.packetsLost / totalPackets : 0;
        
        // Calculate frame drop rate
        const frameDropRate = video.framesReceived > 0 ? 
            video.framesDropped / video.framesReceived : 0;
        
        // Adjust quality based on performance
        if (lossRate > 0.05 || frameDropRate > 0.1 || connection.currentRoundTripTime > 200) {
            // Poor performance - reduce quality
            this.reduceVideoQuality();
        } else if (lossRate < 0.01 && frameDropRate < 0.02 && connection.currentRoundTripTime < 100) {
            // Good performance - increase quality if available bandwidth allows
            if (connection.availableOutgoingBitrate > 2000000) { // 2 Mbps available
                this.increaseVideoQuality();
            }
        }
    }

    reduceVideoQuality() {
        // Get video sender
        const sender = this.peerConnection.getSenders().find(s => 
            s.track && s.track.kind === 'video'
        );
        
        if (sender) {
            const params = sender.getParameters();
            
            // Reduce bitrate for each encoding
            params.encodings.forEach(encoding => {
                if (encoding.maxBitrate) {
                    encoding.maxBitrate = Math.max(encoding.maxBitrate * 0.8, 100000);
                }
                if (encoding.maxFramerate) {
                    encoding.maxFramerate = Math.max(encoding.maxFramerate * 0.8, 15);
                }
            });
            
            sender.setParameters(params);
        }
    }

    increaseVideoQuality() {
        const sender = this.peerConnection.getSenders().find(s => 
            s.track && s.track.kind === 'video'
        );
        
        if (sender) {
            const params = sender.getParameters();
            
            // Increase bitrate for each encoding
            params.encodings.forEach(encoding => {
                if (encoding.maxBitrate) {
                    encoding.maxBitrate = Math.min(encoding.maxBitrate * 1.2, 4000000);
                }
                if (encoding.maxFramerate) {
                    encoding.maxFramerate = Math.min(encoding.maxFramerate * 1.1, 30);
                }
            });
            
            sender.setParameters(params);
        }
    }

    optimizeAudioSettings() {
        const audio = this.stats.audio;
        
        // Enable/disable audio features based on network conditions
        if (audio.jitter > 0.05 || audio.packetsLost > audio.packetsReceived * 0.03) {
            // Poor audio quality - enable more aggressive noise suppression
            this.updateAudioConstraints({
                echoCancellation: true,
                noiseSuppression: true,
                autoGainControl: true,
                sampleRate: 16000  // Reduce to 16kHz for better stability
            });
        }
    }

    async updateAudioConstraints(constraints) {
        const audioTrack = this.peerConnection.getSenders().find(s => 
            s.track && s.track.kind === 'audio'
        )?.track;
        
        if (audioTrack) {
            try {
                await audioTrack.applyConstraints(constraints);
            } catch (error) {
                console.warn('Failed to apply audio constraints:', error);
            }
        }
    }

    startMonitoring(interval = 5000) {
        // Continuously monitor and optimize
        setInterval(() => {
            this.optimizeConnection();
        }, interval);
    }
}

// Usage
const optimizer = new WebRTCOptimizer(peerConnection);
optimizer.startMonitoring();
```

Real-time protocols enable interactive multimedia applications by providing low-latency communication with adaptive quality mechanisms. Understanding these protocols is essential for developing video conferencing, live streaming, and real-time collaboration applications.
