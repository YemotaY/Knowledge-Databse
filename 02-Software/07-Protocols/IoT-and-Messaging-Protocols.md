# IoT and Messaging Protocols (MQTT, CoAP, AMQP)

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

IoT and messaging protocols enable efficient communication between devices, sensors, and services in distributed systems. These protocols are optimized for constrained environments, unreliable networks, and high-volume message exchange.

## Overview

These protocols address specific challenges in IoT and messaging systems: power constraints, limited bandwidth, unreliable connectivity, massive scale, and diverse device capabilities. They provide various communication patterns including publish-subscribe, request-response, and message queuing.

## MQTT (Message Queuing Telemetry Transport)

### MQTT Architecture and Concepts
```
MQTT Publish-Subscribe Architecture:

Publisher 1 ────┐
                │
Publisher 2 ────┼──→ MQTT Broker ──┬──→ Subscriber 1
                │       │           │
Publisher 3 ────┘       │           ├──→ Subscriber 2
                        │           │
                    Topic Store     └──→ Subscriber 3
                        │
                   Retained Msgs
                   Session State
                   Will Messages

Key Concepts:
- Broker: Central message router
- Publisher: Sends messages to topics
- Subscriber: Receives messages from topics
- Topic: Hierarchical message categories
- QoS: Quality of Service levels
- Retain: Last message kept for new subscribers
- Will: Message sent when client disconnects unexpectedly

Topic Hierarchy Example:
home/
├── livingroom/
│   ├── temperature
│   ├── humidity
│   └── light/
│       ├── brightness
│       └── color
├── bedroom/
│   ├── temperature
│   └── humidity
└── garden/
    ├── soil/moisture
    └── weather/
        ├── temperature
        └── rainfall
```

### MQTT Packet Format and Types
```
MQTT Fixed Header:
 7  6  5  4   3  2  1  0
┌──┬──┬──┬──┬──┬──┬──┬──┐
│  Message Type │Flags │
├──┴──┴──┴──┼──┴──┴──┴──┤
│  Remaining Length     │
└───────────────────────┘

Message Types:
1   CONNECT     Client connection request
2   CONNACK     Connection acknowledgment
3   PUBLISH     Publish message
4   PUBACK      Publish acknowledgment (QoS 1)
5   PUBREC      Publish received (QoS 2)
6   PUBREL      Publish release (QoS 2)
7   PUBCOMP     Publish complete (QoS 2)
8   SUBSCRIBE   Subscribe to topics
9   SUBACK      Subscribe acknowledgment
10  UNSUBSCRIBE Unsubscribe from topics
11  UNSUBACK    Unsubscribe acknowledgment
12  PINGREQ     Ping request
13  PINGRESP    Ping response
14  DISCONNECT  Client disconnection
15  AUTH        Authentication exchange (MQTT 5.0)

PUBLISH Packet Structure:
┌─────────────────────────┐
│     Fixed Header        │
├─────────────────────────┤
│     Topic Name          │
├─────────────────────────┤
│     Packet ID (QoS>0)   │
├─────────────────────────┤
│     Properties (v5.0)   │
├─────────────────────────┤
│     Payload             │
└─────────────────────────┘

Quality of Service Levels:
QoS 0: At most once delivery (fire and forget)
QoS 1: At least once delivery (acknowledged)
QoS 2: Exactly once delivery (four-way handshake)

QoS Flow Examples:
QoS 0: Publisher → Broker → Subscriber
QoS 1: Publisher → Broker → Subscriber
              ↙           ↖
           PUBACK        PUBACK
QoS 2: Publisher → Broker → Subscriber
         ↓    ↑     ↓    ↑
       PUBREC PUBREL PUBREC PUBREL
         ↑    ↓     ↑    ↓
       PUBCOMP      PUBCOMP
```

### MQTT Implementation Examples
```python
import paho.mqtt.client as mqtt
import json
import time
import threading
from datetime import datetime
import ssl

class MQTTClient:
    def __init__(self, broker_host, broker_port=1883, client_id=None):
        self.broker_host = broker_host
        self.broker_port = broker_port
        self.client_id = client_id or f"mqtt_client_{int(time.time())}"
        
        # Create MQTT client
        self.client = mqtt.Client(client_id=self.client_id)
        self.client.on_connect = self._on_connect
        self.client.on_disconnect = self._on_disconnect
        self.client.on_message = self._on_message
        self.client.on_publish = self._on_publish
        self.client.on_subscribe = self._on_subscribe
        
        # Message handlers
        self.message_handlers = {}
        self.connected = False
        
    def _on_connect(self, client, userdata, flags, rc):
        """Callback for when client connects to broker"""
        if rc == 0:
            self.connected = True
            print(f"Connected to MQTT broker {self.broker_host}:{self.broker_port}")
            
            # Resubscribe to topics on reconnect
            if hasattr(self, 'subscriptions'):
                for topic, qos in self.subscriptions.items():
                    self.client.subscribe(topic, qos)
        else:
            print(f"Failed to connect to MQTT broker. Return code: {rc}")
            
    def _on_disconnect(self, client, userdata, rc):
        """Callback for when client disconnects from broker"""
        self.connected = False
        if rc != 0:
            print("Unexpected disconnection from MQTT broker")
        else:
            print("Disconnected from MQTT broker")
            
    def _on_message(self, client, userdata, msg):
        """Callback for when a message is received"""
        topic = msg.topic
        payload = msg.payload.decode('utf-8')
        
        print(f"Received message on topic '{topic}': {payload}")
        
        # Call registered handlers
        for pattern, handler in self.message_handlers.items():
            if self._topic_matches(topic, pattern):
                try:
                    # Try to parse as JSON
                    data = json.loads(payload)
                    handler(topic, data)
                except json.JSONDecodeError:
                    # Handle as string
                    handler(topic, payload)
                    
    def _on_publish(self, client, userdata, mid):
        """Callback for when a message is published"""
        print(f"Message {mid} published successfully")
        
    def _on_subscribe(self, client, userdata, mid, granted_qos):
        """Callback for when subscription is acknowledged"""
        print(f"Subscription {mid} granted with QoS {granted_qos}")

    def _topic_matches(self, topic, pattern):
        """Check if topic matches pattern (supports wildcards)"""
        # Simple wildcard matching
        if pattern == topic:
            return True
        if '+' in pattern or '#' in pattern:
            # Implement MQTT wildcard matching
            topic_parts = topic.split('/')
            pattern_parts = pattern.split('/')
            
            if '#' in pattern:
                # Multi-level wildcard
                hash_index = pattern_parts.index('#')
                return topic_parts[:hash_index] == pattern_parts[:hash_index]
            
            if '+' in pattern:
                # Single-level wildcard
                if len(topic_parts) != len(pattern_parts):
                    return False
                for tp, pp in zip(topic_parts, pattern_parts):
                    if pp != '+' and tp != pp:
                        return False
                return True
                
        return False

    def connect(self, username=None, password=None, use_tls=False):
        """Connect to MQTT broker"""
        if username and password:
            self.client.username_pw_set(username, password)
            
        if use_tls:
            self.client.tls_set(ca_certs=None, certfile=None, keyfile=None,
                               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLS,
                               ciphers=None)
            
        try:
            self.client.connect(self.broker_host, self.broker_port, 60)
            self.client.loop_start()  # Start background thread
            
            # Wait for connection
            timeout = 10
            while not self.connected and timeout > 0:
                time.sleep(0.1)
                timeout -= 0.1
                
            return self.connected
        except Exception as e:
            print(f"Failed to connect: {e}")
            return False

    def disconnect(self):
        """Disconnect from MQTT broker"""
        self.client.loop_stop()
        self.client.disconnect()

    def publish(self, topic, payload, qos=0, retain=False):
        """Publish message to topic"""
        if not self.connected:
            print("Not connected to broker")
            return False
            
        if isinstance(payload, dict):
            payload = json.dumps(payload)
            
        result = self.client.publish(topic, payload, qos=qos, retain=retain)
        return result.rc == mqtt.MQTT_ERR_SUCCESS

    def subscribe(self, topic, qos=0, handler=None):
        """Subscribe to topic"""
        if not hasattr(self, 'subscriptions'):
            self.subscriptions = {}
            
        self.subscriptions[topic] = qos
        
        if handler:
            self.message_handlers[topic] = handler
            
        if self.connected:
            result = self.client.subscribe(topic, qos)
            return result[0] == mqtt.MQTT_ERR_SUCCESS
        return False

    def unsubscribe(self, topic):
        """Unsubscribe from topic"""
        if hasattr(self, 'subscriptions') and topic in self.subscriptions:
            del self.subscriptions[topic]
            
        if topic in self.message_handlers:
            del self.message_handlers[topic]
            
        if self.connected:
            result = self.client.unsubscribe(topic)
            return result[0] == mqtt.MQTT_ERR_SUCCESS
        return False

    def set_will(self, topic, payload, qos=0, retain=False):
        """Set last will and testament message"""
        if isinstance(payload, dict):
            payload = json.dumps(payload)
        self.client.will_set(topic, payload, qos, retain)

# IoT Device Simulator
class IoTDevice:
    def __init__(self, device_id, mqtt_client):
        self.device_id = device_id
        self.mqtt_client = mqtt_client
        self.running = False
        self.sensor_data = {}
        
    def start_sensors(self):
        """Start simulated sensor data collection"""
        self.running = True
        
        # Set last will message
        self.mqtt_client.set_will(
            f"devices/{self.device_id}/status",
            json.dumps({"status": "offline", "timestamp": datetime.now().isoformat()}),
            qos=1,
            retain=True
        )
        
        # Publish online status
        self.mqtt_client.publish(
            f"devices/{self.device_id}/status",
            {"status": "online", "timestamp": datetime.now().isoformat()},
            qos=1,
            retain=True
        )
        
        # Start sensor threads
        threading.Thread(target=self._temperature_sensor, daemon=True).start()
        threading.Thread(target=self._humidity_sensor, daemon=True).start()
        threading.Thread(target=self._motion_sensor, daemon=True).start()
        
    def stop_sensors(self):
        """Stop sensor data collection"""
        self.running = False
        
        # Publish offline status
        self.mqtt_client.publish(
            f"devices/{self.device_id}/status",
            {"status": "offline", "timestamp": datetime.now().isoformat()},
            qos=1,
            retain=True
        )
        
    def _temperature_sensor(self):
        """Simulate temperature sensor"""
        import random
        while self.running:
            temperature = 20 + random.uniform(-5, 15)  # 15-35°C
            
            self.mqtt_client.publish(
                f"sensors/{self.device_id}/temperature",
                {
                    "value": round(temperature, 2),
                    "unit": "celsius",
                    "timestamp": datetime.now().isoformat()
                },
                qos=0
            )
            
            time.sleep(30)  # Every 30 seconds
            
    def _humidity_sensor(self):
        """Simulate humidity sensor"""
        import random
        while self.running:
            humidity = random.uniform(30, 80)  # 30-80%
            
            self.mqtt_client.publish(
                f"sensors/{self.device_id}/humidity",
                {
                    "value": round(humidity, 2),
                    "unit": "percent",
                    "timestamp": datetime.now().isoformat()
                },
                qos=0
            )
            
            time.sleep(60)  # Every minute
            
    def _motion_sensor(self):
        """Simulate motion sensor"""
        import random
        while self.running:
            # Random motion events
            if random.random() < 0.1:  # 10% chance per check
                self.mqtt_client.publish(
                    f"sensors/{self.device_id}/motion",
                    {
                        "detected": True,
                        "timestamp": datetime.now().isoformat()
                    },
                    qos=1  # Important events use QoS 1
                )
                
            time.sleep(5)  # Check every 5 seconds

# Usage Example
def sensor_data_handler(topic, data):
    """Handle incoming sensor data"""
    print(f"Sensor data from {topic}: {data}")
    
    # Example: Store in database, trigger alerts, etc.
    if 'temperature' in topic and data['value'] > 30:
        print(f"High temperature alert: {data['value']}°C")

def device_status_handler(topic, data):
    """Handle device status updates"""
    print(f"Device status update: {data}")

# Setup MQTT client
mqtt_client = MQTTClient("localhost", 1883, "iot_hub")

# Connect to broker
if mqtt_client.connect():
    # Subscribe to all sensor data
    mqtt_client.subscribe("sensors/+/+", qos=0, handler=sensor_data_handler)
    
    # Subscribe to device status
    mqtt_client.subscribe("devices/+/status", qos=1, handler=device_status_handler)
    
    # Create and start IoT device
    device = IoTDevice("living_room_001", mqtt_client)
    device.start_sensors()
    
    try:
        # Keep running
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print("Stopping...")
        device.stop_sensors()
        mqtt_client.disconnect()
```

## CoAP (Constrained Application Protocol)

### CoAP Protocol Overview
```
CoAP Message Format:
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│Ver│  T  │   TKL   │      Code         │          Message ID       │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│   Token (if any, TKL bytes) ...                                 │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│   Options (if any) ...                                          │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│1 1 1 1 1 1 1 1│    Payload (if any) ...                        │
└─┴─┴─┴─┴─┴─┴─┴─┴─────────────────────────────────────────────────┘

Field Descriptions:
Ver:        Version (currently 1)
T:          Message Type (CON, NON, ACK, RST)
TKL:        Token Length (0-8 bytes)
Code:       Request/Response code
Message ID: Message identifier for matching
Token:      Client-generated identifier
Options:    Header options (URI-Path, Content-Format, etc.)
Payload:    Message body

Message Types:
CON (0): Confirmable message (requires ACK)
NON (1): Non-confirmable message
ACK (2): Acknowledgment
RST (3): Reset

CoAP vs HTTP Comparison:
Feature              HTTP                CoAP
─────────────────────────────────────────────────────
Transport            TCP                 UDP
Message Size         Large               Small (fits in UDP packet)
Header Overhead      High                Low
Methods              GET,POST,PUT,DELETE GET,POST,PUT,DELETE
Response Codes       Similar             Similar (subset)
Content Negotiation  Headers             Options
Caching              Headers             Options
Reliability          TCP guarantees      Optional (CON messages)
```

### CoAP Implementation
```python
import socket
import struct
import random
import time
from enum import Enum

class CoAPType(Enum):
    CONFIRMABLE = 0
    NON_CONFIRMABLE = 1
    ACKNOWLEDGEMENT = 2
    RESET = 3

class CoAPCode(Enum):
    # Request codes
    GET = 1
    POST = 2
    PUT = 3
    DELETE = 4
    
    # Response codes
    CREATED = 65        # 2.01
    DELETED = 66        # 2.02
    VALID = 67          # 2.03
    CHANGED = 68        # 2.04
    CONTENT = 69        # 2.05
    BAD_REQUEST = 128   # 4.00
    UNAUTHORIZED = 129  # 4.01
    BAD_OPTION = 130    # 4.02
    FORBIDDEN = 131     # 4.03
    NOT_FOUND = 132     # 4.04
    NOT_ALLOWED = 133   # 4.05
    INTERNAL_ERROR = 160 # 5.00
    NOT_IMPLEMENTED = 161 # 5.01
    BAD_GATEWAY = 162   # 5.02
    SERVICE_UNAVAILABLE = 163 # 5.03
    GATEWAY_TIMEOUT = 164 # 5.04

class CoAPOption(Enum):
    IF_MATCH = 1
    URI_HOST = 3
    ETAG = 4
    IF_NONE_MATCH = 5
    URI_PORT = 7
    LOCATION_PATH = 8
    URI_PATH = 11
    CONTENT_FORMAT = 12
    MAX_AGE = 14
    URI_QUERY = 15
    ACCEPT = 17
    LOCATION_QUERY = 20
    PROXY_URI = 35
    PROXY_SCHEME = 39

class CoAPMessage:
    def __init__(self, message_type=CoAPType.CONFIRMABLE, code=CoAPCode.GET, 
                 message_id=None, token=None, options=None, payload=b''):
        self.version = 1
        self.message_type = message_type
        self.code = code
        self.message_id = message_id or random.randint(0, 65535)
        self.token = token or b''
        self.options = options or []
        self.payload = payload

    def pack(self):
        """Pack CoAP message into bytes"""
        # Header
        header = struct.pack('!BBH', 
                           (self.version << 6) | (self.message_type.value << 4) | len(self.token),
                           self.code.value,
                           self.message_id)
        
        # Token
        message = header + self.token
        
        # Options (simplified - would need proper option encoding)
        for option_num, option_value in self.options:
            if isinstance(option_value, str):
                option_value = option_value.encode('utf-8')
            # Simplified option encoding
            message += struct.pack('!BB', option_num, len(option_value)) + option_value
        
        # Payload marker and payload
        if self.payload:
            message += b'\xff' + self.payload
            
        return message

    @classmethod
    def unpack(cls, data):
        """Unpack bytes into CoAP message"""
        if len(data) < 4:
            raise ValueError("Invalid CoAP message length")
        
        # Parse header
        header = struct.unpack('!BBH', data[:4])
        version = (header[0] >> 6) & 0x3
        message_type = CoAPType((header[0] >> 4) & 0x3)
        token_length = header[0] & 0xf
        code = CoAPCode(header[1])
        message_id = header[2]
        
        offset = 4
        
        # Parse token
        token = data[offset:offset + token_length]
        offset += token_length
        
        # Parse options (simplified)
        options = []
        while offset < len(data) and data[offset] != 0xff:
            option_num = data[offset]
            option_length = data[offset + 1]
            option_value = data[offset + 2:offset + 2 + option_length]
            options.append((option_num, option_value))
            offset += 2 + option_length
        
        # Parse payload
        payload = b''
        if offset < len(data) and data[offset] == 0xff:
            payload = data[offset + 1:]
        
        return cls(message_type, code, message_id, token, options, payload)

class CoAPClient:
    def __init__(self, server_host='localhost', server_port=5683):
        self.server_host = server_host
        self.server_port = server_port
        self.socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        self.socket.settimeout(10)  # 10 second timeout

    def send_request(self, method, uri_path, payload=b'', confirmable=True):
        """Send CoAP request"""
        message_type = CoAPType.CONFIRMABLE if confirmable else CoAPType.NON_CONFIRMABLE
        
        # Create request message
        options = [(CoAPOption.URI_PATH.value, uri_path)]
        if payload:
            options.append((CoAPOption.CONTENT_FORMAT.value, b'\x00'))  # text/plain
        
        message = CoAPMessage(
            message_type=message_type,
            code=method,
            options=options,
            payload=payload
        )
        
        # Send message
        data = message.pack()
        self.socket.sendto(data, (self.server_host, self.server_port))
        
        # Wait for response (if confirmable)
        if confirmable:
            try:
                response_data, addr = self.socket.recvfrom(1024)
                response = CoAPMessage.unpack(response_data)
                return response
            except socket.timeout:
                print("Request timed out")
                return None
        
        return None

    def get(self, uri_path):
        """Send GET request"""
        return self.send_request(CoAPCode.GET, uri_path)

    def post(self, uri_path, payload):
        """Send POST request"""
        return self.send_request(CoAPCode.POST, uri_path, payload)

    def put(self, uri_path, payload):
        """Send PUT request"""
        return self.send_request(CoAPCode.PUT, uri_path, payload)

    def delete(self, uri_path):
        """Send DELETE request"""
        return self.send_request(CoAPCode.DELETE, uri_path)

    def close(self):
        """Close socket"""
        self.socket.close()

class CoAPServer:
    def __init__(self, host='localhost', port=5683):
        self.host = host
        self.port = port
        self.socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        self.socket.bind((host, port))
        self.resources = {}
        self.running = False

    def add_resource(self, path, handler):
        """Add resource handler"""
        self.resources[path] = handler

    def start(self):
        """Start CoAP server"""
        self.running = True
        print(f"CoAP server started on {self.host}:{self.port}")
        
        while self.running:
            try:
                data, addr = self.socket.recvfrom(1024)
                self.handle_request(data, addr)
            except Exception as e:
                print(f"Error handling request: {e}")

    def handle_request(self, data, addr):
        """Handle incoming CoAP request"""
        try:
            request = CoAPMessage.unpack(data)
            
            # Extract URI path from options
            uri_path = ""
            for option_num, option_value in request.options:
                if option_num == CoAPOption.URI_PATH.value:
                    if isinstance(option_value, bytes):
                        uri_path = option_value.decode('utf-8')
                    else:
                        uri_path = option_value
                    break
            
            # Find resource handler
            if uri_path in self.resources:
                handler = self.resources[uri_path]
                response = handler(request)
            else:
                # Resource not found
                response = CoAPMessage(
                    message_type=CoAPType.ACKNOWLEDGEMENT,
                    code=CoAPCode.NOT_FOUND,
                    message_id=request.message_id,
                    token=request.token
                )
            
            # Send response
            response_data = response.pack()
            self.socket.sendto(response_data, addr)
            
        except Exception as e:
            print(f"Error processing request: {e}")

    def stop(self):
        """Stop CoAP server"""
        self.running = False
        self.socket.close()

# Example resource handlers
def temperature_handler(request):
    """Handle temperature sensor requests"""
    if request.code == CoAPCode.GET:
        # Simulate temperature reading
        import random
        temperature = 20 + random.uniform(-5, 15)
        payload = f"{temperature:.2f}".encode('utf-8')
        
        return CoAPMessage(
            message_type=CoAPType.ACKNOWLEDGEMENT,
            code=CoAPCode.CONTENT,
            message_id=request.message_id,
            token=request.token,
            payload=payload
        )
    else:
        return CoAPMessage(
            message_type=CoAPType.ACKNOWLEDGEMENT,
            code=CoAPCode.NOT_ALLOWED,
            message_id=request.message_id,
            token=request.token
        )

def led_handler(request):
    """Handle LED control requests"""
    if request.code == CoAPCode.PUT:
        # Control LED
        state = request.payload.decode('utf-8')
        print(f"LED state changed to: {state}")
        
        return CoAPMessage(
            message_type=CoAPType.ACKNOWLEDGEMENT,
            code=CoAPCode.CHANGED,
            message_id=request.message_id,
            token=request.token
        )
    elif request.code == CoAPCode.GET:
        # Get LED state
        payload = b"on"  # Simulate current state
        
        return CoAPMessage(
            message_type=CoAPType.ACKNOWLEDGEMENT,
            code=CoAPCode.CONTENT,
            message_id=request.message_id,
            token=request.token,
            payload=payload
        )
    else:
        return CoAPMessage(
            message_type=CoAPType.ACKNOWLEDGEMENT,
            code=CoAPCode.NOT_ALLOWED,
            message_id=request.message_id,
            token=request.token
        )

# Usage example
if __name__ == "__main__":
    import threading
    
    # Start CoAP server
    server = CoAPServer()
    server.add_resource("temperature", temperature_handler)
    server.add_resource("led", led_handler)
    
    server_thread = threading.Thread(target=server.start, daemon=True)
    server_thread.start()
    
    time.sleep(1)  # Give server time to start
    
    # CoAP client examples
    client = CoAPClient()
    
    # Get temperature
    response = client.get("temperature")
    if response:
        print(f"Temperature: {response.payload.decode('utf-8')}°C")
    
    # Control LED
    response = client.put("led", b"on")
    if response:
        print("LED turned on")
    
    # Get LED state
    response = client.get("led")
    if response:
        print(f"LED state: {response.payload.decode('utf-8')}")
    
    client.close()
```

## AMQP (Advanced Message Queuing Protocol)

### AMQP Architecture and Concepts
```
AMQP Message Flow:

Producer ──→ Exchange ──→ Queue ──→ Consumer
              │    ↑       │
              │    │       │
         Routing   │   Binding
          Rules    │    Rules
              │    │       │
              └────┴───────┘

AMQP Components:
- Producer: Sends messages to exchanges
- Exchange: Routes messages to queues based on routing rules
- Queue: Stores messages until consumed
- Consumer: Receives and processes messages
- Binding: Links exchanges to queues with routing keys
- Virtual Host: Namespace for AMQP entities

Exchange Types:
1. Direct Exchange:
   - Routes messages based on exact routing key match
   - Point-to-point messaging

2. Topic Exchange:
   - Routes based on wildcard pattern matching
   - Publish-subscribe with filtering

3. Fanout Exchange:
   - Routes to all bound queues (ignores routing key)
   - Broadcast messaging

4. Headers Exchange:
   - Routes based on message headers instead of routing key

Message Properties:
- Delivery Mode: Persistent/transient
- Priority: Message priority (0-9)
- Timestamp: Message creation time
- Message ID: Unique identifier
- Content Type: MIME type
- Content Encoding: Compression/encoding
- Headers: Custom headers
- Expiration: Message TTL
```

### AMQP Implementation with RabbitMQ
```python
import pika
import json
import threading
import time
from datetime import datetime
import logging

class AMQPProducer:
    def __init__(self, connection_params=None):
        if connection_params is None:
            connection_params = pika.ConnectionParameters('localhost')
        
        self.connection_params = connection_params
        self.connection = None
        self.channel = None
        
    def connect(self):
        """Connect to RabbitMQ broker"""
        try:
            self.connection = pika.BlockingConnection(self.connection_params)
            self.channel = self.connection.channel()
            return True
        except Exception as e:
            logging.error(f"Failed to connect to AMQP broker: {e}")
            return False
    
    def declare_exchange(self, exchange_name, exchange_type='direct', durable=True):
        """Declare an exchange"""
        if self.channel:
            self.channel.exchange_declare(
                exchange=exchange_name,
                exchange_type=exchange_type,
                durable=durable
            )
    
    def declare_queue(self, queue_name, durable=True, exclusive=False, auto_delete=False):
        """Declare a queue"""
        if self.channel:
            return self.channel.queue_declare(
                queue=queue_name,
                durable=durable,
                exclusive=exclusive,
                auto_delete=auto_delete
            )
    
    def bind_queue(self, queue_name, exchange_name, routing_key=''):
        """Bind queue to exchange"""
        if self.channel:
            self.channel.queue_bind(
                exchange=exchange_name,
                queue=queue_name,
                routing_key=routing_key
            )
    
    def publish_message(self, exchange, routing_key, message, properties=None):
        """Publish message to exchange"""
        if not self.channel:
            if not self.connect():
                return False
        
        if isinstance(message, dict):
            message = json.dumps(message)
        
        # Default message properties
        if properties is None:
            properties = pika.BasicProperties(
                delivery_mode=2,  # Persistent
                timestamp=int(time.time()),
                content_type='application/json'
            )
        
        try:
            self.channel.basic_publish(
                exchange=exchange,
                routing_key=routing_key,
                body=message,
                properties=properties
            )
            return True
        except Exception as e:
            logging.error(f"Failed to publish message: {e}")
            return False
    
    def close(self):
        """Close connection"""
        if self.connection and not self.connection.is_closed:
            self.connection.close()

class AMQPConsumer:
    def __init__(self, connection_params=None):
        if connection_params is None:
            connection_params = pika.ConnectionParameters('localhost')
        
        self.connection_params = connection_params
        self.connection = None
        self.channel = None
        self.consuming = False
        
    def connect(self):
        """Connect to RabbitMQ broker"""
        try:
            self.connection = pika.BlockingConnection(self.connection_params)
            self.channel = self.connection.channel()
            return True
        except Exception as e:
            logging.error(f"Failed to connect to AMQP broker: {e}")
            return False
    
    def declare_queue(self, queue_name, durable=True, exclusive=False, auto_delete=False):
        """Declare a queue"""
        if self.channel:
            return self.channel.queue_declare(
                queue=queue_name,
                durable=durable,
                exclusive=exclusive,
                auto_delete=auto_delete
            )
    
    def consume_messages(self, queue_name, callback, auto_ack=False):
        """Start consuming messages from queue"""
        if not self.channel:
            if not self.connect():
                return False
        
        # Set QoS to process one message at a time
        self.channel.basic_qos(prefetch_count=1)
        
        def wrapper(ch, method, properties, body):
            """Wrapper for message callback"""
            try:
                # Decode message
                message = body.decode('utf-8')
                try:
                    message = json.loads(message)
                except json.JSONDecodeError:
                    pass
                
                # Call user callback
                result = callback(message, method, properties)
                
                # Acknowledge message if not auto-ack
                if not auto_ack:
                    if result is not False:  # Any result except False = success
                        ch.basic_ack(delivery_tag=method.delivery_tag)
                    else:
                        ch.basic_nack(delivery_tag=method.delivery_tag, requeue=True)
                        
            except Exception as e:
                logging.error(f"Error processing message: {e}")
                if not auto_ack:
                    ch.basic_nack(delivery_tag=method.delivery_tag, requeue=True)
        
        self.channel.basic_consume(
            queue=queue_name,
            on_message_callback=wrapper,
            auto_ack=auto_ack
        )
        
        self.consuming = True
        print(f"Started consuming from queue: {queue_name}")
        
        try:
            self.channel.start_consuming()
        except KeyboardInterrupt:
            self.stop_consuming()
    
    def stop_consuming(self):
        """Stop consuming messages"""
        if self.channel and self.consuming:
            self.channel.stop_consuming()
            self.consuming = False
    
    def close(self):
        """Close connection"""
        if self.connection and not self.connection.is_closed:
            self.connection.close()

# Message Processing Examples
class TaskProcessor:
    def __init__(self):
        self.producer = AMQPProducer()
        self.consumer = AMQPConsumer()
        
    def setup_task_queue(self):
        """Setup task queue infrastructure"""
        if not self.producer.connect():
            return False
        
        # Declare exchanges and queues
        self.producer.declare_exchange('tasks', 'direct', durable=True)
        self.producer.declare_queue('task_queue', durable=True)
        self.producer.bind_queue('task_queue', 'tasks', 'process')
        
        # Declare dead letter queue for failed tasks
        self.producer.declare_exchange('dead_letter', 'direct', durable=True)
        self.producer.declare_queue('failed_tasks', durable=True)
        self.producer.bind_queue('failed_tasks', 'dead_letter', 'failed')
        
        return True
    
    def submit_task(self, task_type, task_data, priority=0):
        """Submit task for processing"""
        message = {
            'task_type': task_type,
            'task_data': task_data,
            'submitted_at': datetime.now().isoformat(),
            'task_id': f"{task_type}_{int(time.time() * 1000)}"
        }
        
        properties = pika.BasicProperties(
            delivery_mode=2,  # Persistent
            priority=priority,
            timestamp=int(time.time()),
            content_type='application/json'
        )
        
        return self.producer.publish_message(
            exchange='tasks',
            routing_key='process',
            message=message,
            properties=properties
        )
    
    def process_tasks(self):
        """Start processing tasks"""
        if not self.consumer.connect():
            return False
        
        def task_handler(message, method, properties):
            """Handle individual task"""
            try:
                print(f"Processing task: {message['task_id']}")
                
                # Simulate task processing
                task_type = message['task_type']
                task_data = message['task_data']
                
                if task_type == 'email':
                    self._send_email(task_data)
                elif task_type == 'image_resize':
                    self._resize_image(task_data)
                elif task_type == 'data_backup':
                    self._backup_data(task_data)
                else:
                    print(f"Unknown task type: {task_type}")
                    return False
                
                print(f"Task {message['task_id']} completed successfully")
                return True
                
            except Exception as e:
                print(f"Task {message['task_id']} failed: {e}")
                
                # Send to dead letter queue
                self._send_to_dead_letter(message, str(e))
                return False
        
        self.consumer.declare_queue('task_queue', durable=True)
        self.consumer.consume_messages('task_queue', task_handler, auto_ack=False)
    
    def _send_email(self, data):
        """Simulate email sending"""
        time.sleep(2)  # Simulate processing time
        print(f"Email sent to {data.get('recipient', 'unknown')}")
    
    def _resize_image(self, data):
        """Simulate image resizing"""
        time.sleep(5)  # Simulate processing time
        print(f"Image {data.get('filename', 'unknown')} resized")
    
    def _backup_data(self, data):
        """Simulate data backup"""
        time.sleep(10)  # Simulate processing time
        print(f"Data backup completed for {data.get('source', 'unknown')}")
    
    def _send_to_dead_letter(self, message, error):
        """Send failed task to dead letter queue"""
        failed_message = {
            'original_message': message,
            'error': error,
            'failed_at': datetime.now().isoformat()
        }
        
        self.producer.publish_message(
            exchange='dead_letter',
            routing_key='failed',
            message=failed_message
        )

# Usage Example
def publish_subscribe_example():
    """Example of publish-subscribe pattern using topic exchange"""
    
    # Setup producer
    producer = AMQPProducer()
    if not producer.connect():
        return
    
    # Declare topic exchange
    producer.declare_exchange('logs', 'topic', durable=True)
    
    # Declare queues for different log levels
    producer.declare_queue('error_logs', durable=True)
    producer.declare_queue('warning_logs', durable=True)
    producer.declare_queue('all_logs', durable=True)
    
    # Bind queues with routing patterns
    producer.bind_queue('error_logs', 'logs', '*.error')
    producer.bind_queue('warning_logs', 'logs', '*.warning')
    producer.bind_queue('all_logs', 'logs', '*.*')
    
    # Publish log messages
    log_messages = [
        ('app.error', {'level': 'error', 'message': 'Database connection failed'}),
        ('app.warning', {'level': 'warning', 'message': 'High memory usage detected'}),
        ('system.error', {'level': 'error', 'message': 'Disk space low'}),
        ('app.info', {'level': 'info', 'message': 'User logged in'})
    ]
    
    for routing_key, message in log_messages:
        producer.publish_message('logs', routing_key, message)
        print(f"Published log: {routing_key} - {message['message']}")
    
    producer.close()

if __name__ == "__main__":
    # Example: Task processing system
    processor = TaskProcessor()
    
    if processor.setup_task_queue():
        # Submit some tasks
        processor.submit_task('email', {'recipient': 'user@example.com', 'subject': 'Test'})
        processor.submit_task('image_resize', {'filename': 'photo.jpg', 'size': '800x600'})
        processor.submit_task('data_backup', {'source': '/home/user/documents'})
        
        # Start processing (this will block)
        print("Starting task processor...")
        processor.process_tasks()
```

IoT and messaging protocols enable efficient communication in distributed systems, from tiny sensors to enterprise message brokers. Understanding these protocols is essential for building scalable IoT systems, microservices architectures, and real-time data processing pipelines.
