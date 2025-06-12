# WebSocket and API Protocols

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🌐 [Protocols](README.md)**

WebSocket and modern API protocols enable real-time, bidirectional communication and efficient data exchange between clients and servers, powering modern web applications, real-time collaboration tools, and microservices architectures.

## Overview

These protocols bridge the gap between traditional request-response patterns and real-time communication needs, offering solutions for live updates, streaming data, efficient API calls, and high-performance distributed systems.

## WebSocket Protocol

### WebSocket Architecture and Handshake
```
WebSocket Connection Lifecycle:

1. HTTP Upgrade Request:
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Sec-WebSocket-Protocol: chat, superchat

2. HTTP Upgrade Response:
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat

3. WebSocket Communication:
Client ↔ Server: Bidirectional frame exchange

4. Connection Close:
Close frame with status code and reason

WebSocket vs HTTP Comparison:
Feature              HTTP/1.1         WebSocket
──────────────────────────────────────────────────
Connection Type      Request/Response  Persistent
Overhead per Message High (headers)    Low (2-14 bytes)
Real-time Support    Polling required  Native
Server Initiated     No               Yes
Protocol Switching   No               After handshake
Firewall Friendly    Yes              Yes (port 80/443)
```

### WebSocket Frame Format
```
WebSocket Frame Structure:
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│F│R│R│R│  Opcode   │M│  Payload Length   │    Extended Length  │
│I│S│S│S│   (4)     │A│      (7)          │         (16/64)     │
│N│V│V│V│           │S│                   │                     │
│ │1│2│3│           │K│                   │                     │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                    Masking Key (32)                            │
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
│                         Payload Data                           │
└─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┴─┘

Frame Fields:
FIN:        Final fragment flag
RSV1-3:     Reserved for extensions
Opcode:     Frame type
MASK:       Masking flag (1 for client frames)
Payload Length: Message length
Masking Key: XOR mask for payload (client only)
Payload Data: Actual message content

WebSocket Opcodes:
0x0  Continuation frame
0x1  Text frame (UTF-8)
0x2  Binary frame
0x3-0x7  Reserved for data frames
0x8  Connection close
0x9  Ping
0xA  Pong
0xB-0xF  Reserved for control frames

Message Types:
Text:    UTF-8 encoded strings
Binary:  Raw binary data
Close:   Connection termination
Ping:    Keep-alive from sender
Pong:    Keep-alive response
```

### WebSocket Implementation Examples
```javascript
// Client-side WebSocket Implementation

class WebSocketManager {
    constructor(url, protocols = []) {
        this.url = url;
        this.protocols = protocols;
        this.socket = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
        this.reconnectDelay = 1000;
        this.messageQueue = [];
        this.eventHandlers = {};
    }

    connect() {
        try {
            this.socket = new WebSocket(this.url, this.protocols);
            this.setupEventHandlers();
        } catch (error) {
            console.error('WebSocket connection failed:', error);
            this.handleReconnect();
        }
    }

    setupEventHandlers() {
        this.socket.onopen = (event) => {
            console.log('WebSocket connected');
            this.reconnectAttempts = 0;
            this.sendQueuedMessages();
            this.emit('connected', event);
        };

        this.socket.onmessage = (event) => {
            try {
                const data = JSON.parse(event.data);
                this.handleMessage(data);
            } catch (error) {
                // Handle non-JSON messages
                this.handleMessage(event.data);
            }
        };

        this.socket.onclose = (event) => {
            console.log(`WebSocket closed: ${event.code} - ${event.reason}`);
            this.emit('disconnected', event);
            
            if (!event.wasClean && this.reconnectAttempts < this.maxReconnectAttempts) {
                this.handleReconnect();
            }
        };

        this.socket.onerror = (error) => {
            console.error('WebSocket error:', error);
            this.emit('error', error);
        };
    }

    handleMessage(data) {
        if (typeof data === 'object' && data.type) {
            // Structured message
            switch (data.type) {
                case 'chat':
                    this.emit('chat', data.payload);
                    break;
                case 'notification':
                    this.emit('notification', data.payload);
                    break;
                case 'system':
                    this.handleSystemMessage(data.payload);
                    break;
                default:
                    this.emit('message', data);
            }
        } else {
            // Raw message
            this.emit('message', data);
        }
    }

    handleSystemMessage(payload) {
        switch (payload.command) {
            case 'ping':
                this.send({ type: 'system', payload: { command: 'pong' } });
                break;
            case 'reconnect':
                this.socket.close();
                setTimeout(() => this.connect(), 1000);
                break;
        }
    }

    send(data) {
        if (this.socket && this.socket.readyState === WebSocket.OPEN) {
            const message = typeof data === 'string' ? data : JSON.stringify(data);
            this.socket.send(message);
        } else {
            // Queue message if not connected
            this.messageQueue.push(data);
        }
    }

    sendQueuedMessages() {
        while (this.messageQueue.length > 0) {
            const message = this.messageQueue.shift();
            this.send(message);
        }
    }

    handleReconnect() {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
            this.reconnectAttempts++;
            const delay = this.reconnectDelay * Math.pow(2, this.reconnectAttempts - 1);
            
            console.log(`Reconnecting in ${delay}ms (attempt ${this.reconnectAttempts})`);
            setTimeout(() => this.connect(), delay);
        } else {
            console.error('Max reconnection attempts reached');
            this.emit('maxReconnectAttemptsReached');
        }
    }

    on(event, handler) {
        if (!this.eventHandlers[event]) {
            this.eventHandlers[event] = [];
        }
        this.eventHandlers[event].push(handler);
    }

    emit(event, data) {
        if (this.eventHandlers[event]) {
            this.eventHandlers[event].forEach(handler => handler(data));
        }
    }

    close(code = 1000, reason = 'Normal closure') {
        if (this.socket) {
            this.socket.close(code, reason);
        }
    }

    // Send different types of messages
    sendChat(message, room = 'general') {
        this.send({
            type: 'chat',
            payload: {
                message: message,
                room: room,
                timestamp: Date.now()
            }
        });
    }

    sendBinary(arrayBuffer) {
        if (this.socket && this.socket.readyState === WebSocket.OPEN) {
            this.socket.send(arrayBuffer);
        }
    }

    ping() {
        this.send({
            type: 'system',
            payload: { command: 'ping', timestamp: Date.now() }
        });
    }
}

// Usage Example
const wsManager = new WebSocketManager('wss://api.example.com/ws', ['chat-v1']);

wsManager.on('connected', () => {
    console.log('Connected to WebSocket server');
    wsManager.sendChat('Hello, World!');
});

wsManager.on('chat', (data) => {
    console.log(`Chat message: ${data.message}`);
});

wsManager.on('notification', (data) => {
    console.log(`Notification: ${data.title} - ${data.body}`);
});

wsManager.connect();
```

### Server-side WebSocket Implementation
```python
import asyncio
import websockets
import json
import logging
from typing import Set, Dict, Any
from datetime import datetime

class WebSocketServer:
    def __init__(self, host='localhost', port=8765):
        self.host = host
        self.port = port
        self.clients: Set[websockets.WebSocketServerProtocol] = set()
        self.rooms: Dict[str, Set[websockets.WebSocketServerProtocol]] = {}
        self.client_info: Dict[websockets.WebSocketServerProtocol, Dict[str, Any]] = {}

    async def register_client(self, websocket, path):
        """Register a new client connection"""
        self.clients.add(websocket)
        self.client_info[websocket] = {
            'connected_at': datetime.now(),
            'path': path,
            'rooms': set()
        }
        logging.info(f"Client connected: {websocket.remote_address}")

    async def unregister_client(self, websocket):
        """Unregister a client connection"""
        self.clients.discard(websocket)
        
        # Remove from all rooms
        if websocket in self.client_info:
            for room in self.client_info[websocket]['rooms']:
                if room in self.rooms:
                    self.rooms[room].discard(websocket)
            del self.client_info[websocket]
        
        logging.info(f"Client disconnected: {websocket.remote_address}")

    async def join_room(self, websocket, room_name):
        """Add client to a room"""
        if room_name not in self.rooms:
            self.rooms[room_name] = set()
        
        self.rooms[room_name].add(websocket)
        if websocket in self.client_info:
            self.client_info[websocket]['rooms'].add(room_name)
        
        # Notify room members
        await self.broadcast_to_room(room_name, {
            'type': 'system',
            'payload': {
                'command': 'user_joined',
                'room': room_name
            }
        }, exclude=websocket)

    async def leave_room(self, websocket, room_name):
        """Remove client from a room"""
        if room_name in self.rooms:
            self.rooms[room_name].discard(websocket)
            
            if websocket in self.client_info:
                self.client_info[websocket]['rooms'].discard(room_name)
            
            # Notify room members
            await self.broadcast_to_room(room_name, {
                'type': 'system',
                'payload': {
                    'command': 'user_left',
                    'room': room_name
                }
            })

    async def broadcast_to_all(self, message):
        """Broadcast message to all connected clients"""
        if self.clients:
            await asyncio.gather(
                *[self.send_to_client(client, message) for client in self.clients],
                return_exceptions=True
            )

    async def broadcast_to_room(self, room_name, message, exclude=None):
        """Broadcast message to all clients in a room"""
        if room_name in self.rooms:
            clients = self.rooms[room_name]
            if exclude:
                clients = clients - {exclude}
            
            if clients:
                await asyncio.gather(
                    *[self.send_to_client(client, message) for client in clients],
                    return_exceptions=True
                )

    async def send_to_client(self, websocket, message):
        """Send message to a specific client"""
        try:
            if isinstance(message, dict):
                message = json.dumps(message)
            await websocket.send(message)
        except websockets.exceptions.ConnectionClosed:
            await self.unregister_client(websocket)
        except Exception as e:
            logging.error(f"Error sending to client: {e}")

    async def handle_message(self, websocket, message):
        """Handle incoming message from client"""
        try:
            data = json.loads(message)
            message_type = data.get('type')
            payload = data.get('payload', {})

            if message_type == 'chat':
                await self.handle_chat_message(websocket, payload)
            elif message_type == 'system':
                await self.handle_system_message(websocket, payload)
            elif message_type == 'room':
                await self.handle_room_message(websocket, payload)
            else:
                logging.warning(f"Unknown message type: {message_type}")

        except json.JSONDecodeError:
            await self.send_to_client(websocket, {
                'type': 'error',
                'payload': {'message': 'Invalid JSON format'}
            })
        except Exception as e:
            logging.error(f"Error handling message: {e}")

    async def handle_chat_message(self, websocket, payload):
        """Handle chat messages"""
        room = payload.get('room', 'general')
        message = payload.get('message', '')
        
        if not message.strip():
            return

        # Broadcast to room
        response = {
            'type': 'chat',
            'payload': {
                'message': message,
                'room': room,
                'timestamp': datetime.now().isoformat(),
                'sender': str(websocket.remote_address)
            }
        }
        
        await self.broadcast_to_room(room, response)

    async def handle_system_message(self, websocket, payload):
        """Handle system messages"""
        command = payload.get('command')
        
        if command == 'ping':
            await self.send_to_client(websocket, {
                'type': 'system',
                'payload': {'command': 'pong', 'timestamp': datetime.now().isoformat()}
            })
        elif command == 'get_stats':
            stats = {
                'total_clients': len(self.clients),
                'total_rooms': len(self.rooms),
                'rooms': {room: len(clients) for room, clients in self.rooms.items()}
            }
            await self.send_to_client(websocket, {
                'type': 'system',
                'payload': {'command': 'stats', 'data': stats}
            })

    async def handle_room_message(self, websocket, payload):
        """Handle room-related messages"""
        action = payload.get('action')
        room_name = payload.get('room')
        
        if not room_name:
            return

        if action == 'join':
            await self.join_room(websocket, room_name)
        elif action == 'leave':
            await self.leave_room(websocket, room_name)

    async def client_handler(self, websocket, path):
        """Handle individual client connections"""
        await self.register_client(websocket, path)
        try:
            # Send welcome message
            await self.send_to_client(websocket, {
                'type': 'system',
                'payload': {
                    'command': 'welcome',
                    'message': 'Connected to WebSocket server',
                    'timestamp': datetime.now().isoformat()
                }
            })

            async for message in websocket:
                await self.handle_message(websocket, message)
                
        except websockets.exceptions.ConnectionClosed:
            pass
        except Exception as e:
            logging.error(f"Error in client handler: {e}")
        finally:
            await self.unregister_client(websocket)

    def start_server(self):
        """Start the WebSocket server"""
        logging.info(f"Starting WebSocket server on {self.host}:{self.port}")
        return websockets.serve(self.client_handler, self.host, self.port)

# Usage
async def main():
    server = WebSocketServer('localhost', 8765)
    start_server = server.start_server()
    
    await start_server
    print("WebSocket server started on ws://localhost:8765")
    await asyncio.Future()  # Run forever

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO)
    asyncio.run(main())
```

## Server-Sent Events (SSE)

### SSE Implementation and Usage
```javascript
// Server-Sent Events Client Implementation

class SSEManager {
    constructor(url, options = {}) {
        this.url = url;
        this.options = {
            withCredentials: false,
            reconnectInterval: 1000,
            maxReconnectAttempts: 5,
            ...options
        };
        this.eventSource = null;
        this.reconnectAttempts = 0;
        this.eventHandlers = {};
    }

    connect() {
        try {
            this.eventSource = new EventSource(this.url, {
                withCredentials: this.options.withCredentials
            });

            this.eventSource.onopen = (event) => {
                console.log('SSE connection opened');
                this.reconnectAttempts = 0;
                this.emit('connected', event);
            };

            this.eventSource.onmessage = (event) => {
                try {
                    const data = JSON.parse(event.data);
                    this.emit('message', data);
                } catch (error) {
                    this.emit('message', event.data);
                }
            };

            this.eventSource.onerror = (event) => {
                console.error('SSE connection error:', event);
                this.emit('error', event);
                this.handleReconnect();
            };

            // Listen for custom events
            this.setupCustomEventListeners();

        } catch (error) {
            console.error('Failed to create SSE connection:', error);
        }
    }

    setupCustomEventListeners() {
        // Example custom event types
        const customEvents = ['notification', 'update', 'alert', 'heartbeat'];
        
        customEvents.forEach(eventType => {
            this.eventSource.addEventListener(eventType, (event) => {
                try {
                    const data = JSON.parse(event.data);
                    this.emit(eventType, data);
                } catch (error) {
                    this.emit(eventType, event.data);
                }
            });
        });
    }

    handleReconnect() {
        if (this.reconnectAttempts < this.options.maxReconnectAttempts) {
            this.reconnectAttempts++;
            const delay = this.options.reconnectInterval * Math.pow(2, this.reconnectAttempts - 1);
            
            console.log(`Reconnecting SSE in ${delay}ms (attempt ${this.reconnectAttempts})`);
            setTimeout(() => {
                this.close();
                this.connect();
            }, delay);
        } else {
            console.error('Max SSE reconnection attempts reached');
            this.emit('maxReconnectAttemptsReached');
        }
    }

    on(event, handler) {
        if (!this.eventHandlers[event]) {
            this.eventHandlers[event] = [];
        }
        this.eventHandlers[event].push(handler);
    }

    emit(event, data) {
        if (this.eventHandlers[event]) {
            this.eventHandlers[event].forEach(handler => handler(data));
        }
    }

    close() {
        if (this.eventSource) {
            this.eventSource.close();
            this.eventSource = null;
        }
    }
}

// Usage
const sse = new SSEManager('/api/events', {
    reconnectInterval: 2000,
    maxReconnectAttempts: 10
});

sse.on('connected', () => {
    console.log('Connected to event stream');
});

sse.on('notification', (data) => {
    console.log('Notification:', data);
});

sse.on('update', (data) => {
    console.log('Update received:', data);
});

sse.connect();
```

### SSE Server Implementation
```python
from flask import Flask, Response, request
import json
import time
import threading
from datetime import datetime
from queue import Queue

app = Flask(__name__)

class SSEBroadcaster:
    def __init__(self):
        self.clients = {}
        self.message_queue = Queue()
        self.running = True
        
        # Start background thread for message processing
        self.worker_thread = threading.Thread(target=self._message_worker, daemon=True)
        self.worker_thread.start()

    def add_client(self, client_id, client_queue):
        """Add a new SSE client"""
        self.clients[client_id] = client_queue
        print(f"Client {client_id} connected. Total clients: {len(self.clients)}")

    def remove_client(self, client_id):
        """Remove an SSE client"""
        if client_id in self.clients:
            del self.clients[client_id]
            print(f"Client {client_id} disconnected. Total clients: {len(self.clients)}")

    def broadcast(self, event_type, data, target_clients=None):
        """Broadcast message to all or specific clients"""
        message = {
            'event': event_type,
            'data': data,
            'timestamp': datetime.now().isoformat(),
            'target_clients': target_clients
        }
        self.message_queue.put(message)

    def _message_worker(self):
        """Background worker to process and send messages"""
        while self.running:
            try:
                message = self.message_queue.get(timeout=1)
                self._send_to_clients(message)
                self.message_queue.task_done()
            except:
                continue

    def _send_to_clients(self, message):
        """Send message to appropriate clients"""
        event_type = message['event']
        data = message['data']
        target_clients = message.get('target_clients')
        
        # Determine which clients to send to
        if target_clients:
            clients_to_notify = {k: v for k, v in self.clients.items() if k in target_clients}
        else:
            clients_to_notify = self.clients.copy()

        # Format SSE message
        sse_message = self._format_sse_message(event_type, data)
        
        # Send to clients
        for client_id, client_queue in clients_to_notify.items():
            try:
                client_queue.put(sse_message)
            except:
                # Client queue is closed, remove it
                self.remove_client(client_id)

    def _format_sse_message(self, event_type, data):
        """Format message according to SSE specification"""
        if isinstance(data, dict):
            data_str = json.dumps(data)
        else:
            data_str = str(data)
        
        # SSE format: event: type\ndata: content\n\n
        return f"event: {event_type}\ndata: {data_str}\n\n"

    def send_heartbeat(self):
        """Send heartbeat to all clients"""
        self.broadcast('heartbeat', {'timestamp': datetime.now().isoformat()})

# Global broadcaster instance
broadcaster = SSEBroadcaster()

@app.route('/api/events')
def event_stream():
    """SSE endpoint"""
    client_id = request.args.get('client_id', f"client_{int(time.time() * 1000)}")
    client_queue = Queue()
    
    def generate():
        # Add client to broadcaster
        broadcaster.add_client(client_id, client_queue)
        
        # Send initial connection message
        initial_message = broadcaster._format_sse_message('connected', {
            'client_id': client_id,
            'timestamp': datetime.now().isoformat()
        })
        yield initial_message
        
        try:
            while True:
                # Wait for message from broadcaster
                try:
                    message = client_queue.get(timeout=30)  # 30-second timeout
                    yield message
                except:
                    # Send heartbeat on timeout
                    heartbeat = broadcaster._format_sse_message('heartbeat', {
                        'timestamp': datetime.now().isoformat()
                    })
                    yield heartbeat
        except GeneratorExit:
            # Client disconnected
            broadcaster.remove_client(client_id)
    
    return Response(
        generate(),
        mimetype='text/event-stream',
        headers={
            'Cache-Control': 'no-cache',
            'Connection': 'keep-alive',
            'Access-Control-Allow-Origin': '*',
            'Access-Control-Allow-Headers': 'Cache-Control'
        }
    )

@app.route('/api/broadcast', methods=['POST'])
def broadcast_message():
    """API endpoint to broadcast messages"""
    data = request.get_json()
    event_type = data.get('event', 'message')
    message = data.get('data', {})
    target_clients = data.get('target_clients')
    
    broadcaster.broadcast(event_type, message, target_clients)
    
    return {'status': 'sent', 'clients': len(broadcaster.clients)}

@app.route('/api/clients')
def get_clients():
    """Get connected clients count"""
    return {'client_count': len(broadcaster.clients)}

# Example usage routes
@app.route('/api/notify', methods=['POST'])
def send_notification():
    """Send notification to all clients"""
    data = request.get_json()
    broadcaster.broadcast('notification', {
        'title': data.get('title', 'Notification'),
        'message': data.get('message', ''),
        'priority': data.get('priority', 'normal')
    })
    return {'status': 'notification sent'}

@app.route('/api/update', methods=['POST'])
def send_update():
    """Send update to specific clients"""
    data = request.get_json()
    broadcaster.broadcast('update', data.get('update_data', {}), 
                         data.get('target_clients'))
    return {'status': 'update sent'}

if __name__ == '__main__':
    # Start heartbeat timer
    def heartbeat_timer():
        while True:
            time.sleep(30)  # Send heartbeat every 30 seconds
            broadcaster.send_heartbeat()
    
    heartbeat_thread = threading.Thread(target=heartbeat_timer, daemon=True)
    heartbeat_thread.start()
    
    app.run(debug=True, threaded=True)
```

## GraphQL Protocol

### GraphQL Implementation
```javascript
// GraphQL Client Implementation

class GraphQLClient {
    constructor(endpoint, options = {}) {
        this.endpoint = endpoint;
        this.options = {
            headers: {
                'Content-Type': 'application/json',
                ...options.headers
            },
            credentials: options.credentials || 'same-origin',
            ...options
        };
    }

    async query(query, variables = {}, operationName = null) {
        """Execute GraphQL query"""
        const body = {
            query: query,
            variables: variables
        };

        if (operationName) {
            body.operationName = operationName;
        }

        try {
            const response = await fetch(this.endpoint, {
                method: 'POST',
                headers: this.options.headers,
                credentials: this.options.credentials,
                body: JSON.stringify(body)
            });

            const result = await response.json();

            if (result.errors) {
                throw new GraphQLError('GraphQL errors', result.errors);
            }

            return result.data;
        } catch (error) {
            throw new GraphQLError('Network error', [error.message]);
        }
    }

    async mutation(mutation, variables = {}) {
        """Execute GraphQL mutation"""
        return this.query(mutation, variables);
    }

    // Subscription using WebSocket
    subscribe(subscription, variables = {}) {
        // Implementation would use WebSocket with GraphQL subscription protocol
        // This is a simplified example
        const ws = new WebSocket(`${this.endpoint.replace('http', 'ws')}/graphql`, 'graphql-ws');
        
        ws.onopen = () => {
            ws.send(JSON.stringify({
                type: 'connection_init'
            }));
        };

        ws.onmessage = (event) => {
            const message = JSON.parse(event.data);
            // Handle subscription messages
        };

        return ws;
    }

    // Batch multiple queries
    async batch(operations) {
        """Execute multiple GraphQL operations in one request"""
        const body = operations.map(op => ({
            query: op.query,
            variables: op.variables || {},
            operationName: op.operationName || null
        }));

        const response = await fetch(this.endpoint, {
            method: 'POST',
            headers: this.options.headers,
            credentials: this.options.credentials,
            body: JSON.stringify(body)
        });

        const results = await response.json();
        return results;
    }
}

class GraphQLError extends Error {
    constructor(message, errors) {
        super(message);
        this.name = 'GraphQLError';
        this.errors = errors;
    }
}

// Usage Examples
const client = new GraphQLClient('/graphql', {
    headers: {
        'Authorization': 'Bearer ' + localStorage.getItem('token')
    }
});

// Query example
const getUserQuery = `
    query GetUser($id: ID!) {
        user(id: $id) {
            id
            name
            email
            posts {
                id
                title
                content
                createdAt
            }
        }
    }
`;

client.query(getUserQuery, { id: '123' })
    .then(data => console.log('User data:', data.user))
    .catch(error => console.error('Error:', error));

// Mutation example
const createPostMutation = `
    mutation CreatePost($input: CreatePostInput!) {
        createPost(input: $input) {
            id
            title
            content
            author {
                id
                name
            }
        }
    }
`;

client.mutation(createPostMutation, {
    input: {
        title: 'New Post',
        content: 'This is a new post content'
    }
})
.then(data => console.log('Created post:', data.createPost))
.catch(error => console.error('Error:', error));
```

## gRPC Protocol

### gRPC Implementation
```python
# gRPC Service Definition (user_service.proto)
"""
syntax = "proto3";

package user_service;

service UserService {
    rpc GetUser(GetUserRequest) returns (User);
    rpc ListUsers(ListUsersRequest) returns (stream User);
    rpc CreateUser(CreateUserRequest) returns (User);
    rpc UpdateUser(UpdateUserRequest) returns (User);
    rpc DeleteUser(DeleteUserRequest) returns (DeleteUserResponse);
}

message User {
    string id = 1;
    string name = 2;
    string email = 3;
    int32 age = 4;
    repeated string roles = 5;
}

message GetUserRequest {
    string id = 1;
}

message ListUsersRequest {
    int32 page_size = 1;
    string page_token = 2;
    string filter = 3;
}

message CreateUserRequest {
    string name = 1;
    string email = 2;
    int32 age = 3;
    repeated string roles = 4;
}

message UpdateUserRequest {
    string id = 1;
    User user = 2;
}

message DeleteUserRequest {
    string id = 1;
}

message DeleteUserResponse {
    bool success = 1;
    string message = 2;
}
"""

# gRPC Server Implementation
import grpc
from concurrent import futures
import user_service_pb2
import user_service_pb2_grpc
import asyncio
import logging

class UserServiceImpl(user_service_pb2_grpc.UserServiceServicer):
    def __init__(self):
        # In-memory user storage for demo
        self.users = {}
        self.next_id = 1

    def GetUser(self, request, context):
        """Get a single user by ID"""
        user_id = request.id
        
        if user_id not in self.users:
            context.set_code(grpc.StatusCode.NOT_FOUND)
            context.set_details(f'User with ID {user_id} not found')
            return user_service_pb2.User()
        
        return self.users[user_id]

    def ListUsers(self, request, context):
        """Stream multiple users"""
        # Apply filtering and pagination
        users = list(self.users.values())
        
        # Simple filtering by name
        if request.filter:
            users = [u for u in users if request.filter.lower() in u.name.lower()]
        
        # Pagination
        page_size = request.page_size or 10
        start_index = 0
        
        if request.page_token:
            try:
                start_index = int(request.page_token)
            except ValueError:
                start_index = 0
        
        # Stream users
        for i in range(start_index, min(start_index + page_size, len(users))):
            yield users[i]

    def CreateUser(self, request, context):
        """Create a new user"""
        # Validate input
        if not request.name or not request.email:
            context.set_code(grpc.StatusCode.INVALID_ARGUMENT)
            context.set_details('Name and email are required')
            return user_service_pb2.User()
        
        # Check if email already exists
        for user in self.users.values():
            if user.email == request.email:
                context.set_code(grpc.StatusCode.ALREADY_EXISTS)
                context.set_details(f'User with email {request.email} already exists')
                return user_service_pb2.User()
        
        # Create new user
        user_id = str(self.next_id)
        self.next_id += 1
        
        user = user_service_pb2.User(
            id=user_id,
            name=request.name,
            email=request.email,
            age=request.age,
            roles=list(request.roles)
        )
        
        self.users[user_id] = user
        return user

    def UpdateUser(self, request, context):
        """Update an existing user"""
        user_id = request.id
        
        if user_id not in self.users:
            context.set_code(grpc.StatusCode.NOT_FOUND)
            context.set_details(f'User with ID {user_id} not found')
            return user_service_pb2.User()
        
        # Update user
        updated_user = request.user
        updated_user.id = user_id
        self.users[user_id] = updated_user
        
        return updated_user

    def DeleteUser(self, request, context):
        """Delete a user"""
        user_id = request.id
        
        if user_id not in self.users:
            context.set_code(grpc.StatusCode.NOT_FOUND)
            context.set_details(f'User with ID {user_id} not found')
            return user_service_pb2.DeleteUserResponse(
                success=False,
                message=f'User with ID {user_id} not found'
            )
        
        del self.users[user_id]
        return user_service_pb2.DeleteUserResponse(
            success=True,
            message=f'User {user_id} deleted successfully'
        )

def serve():
    """Start gRPC server"""
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    
    # Add service to server
    user_service_pb2_grpc.add_UserServiceServicer_to_server(
        UserServiceImpl(), server
    )
    
    # Listen on port 50051
    listen_addr = '[::]:50051'
    server.add_insecure_port(listen_addr)
    
    server.start()
    print(f"gRPC server started on {listen_addr}")
    
    try:
        server.wait_for_termination()
    except KeyboardInterrupt:
        server.stop(0)

# gRPC Client Implementation
class UserServiceClient:
    def __init__(self, server_address='localhost:50051'):
        self.channel = grpc.insecure_channel(server_address)
        self.stub = user_service_pb2_grpc.UserServiceStub(self.channel)

    def get_user(self, user_id):
        """Get user by ID"""
        request = user_service_pb2.GetUserRequest(id=user_id)
        try:
            response = self.stub.GetUser(request)
            return response
        except grpc.RpcError as e:
            print(f"gRPC error: {e.code()} - {e.details()}")
            return None

    def list_users(self, page_size=10, page_token="", filter_text=""):
        """List users with streaming"""
        request = user_service_pb2.ListUsersRequest(
            page_size=page_size,
            page_token=page_token,
            filter=filter_text
        )
        
        try:
            users = []
            for user in self.stub.ListUsers(request):
                users.append(user)
            return users
        except grpc.RpcError as e:
            print(f"gRPC error: {e.code()} - {e.details()}")
            return []

    def create_user(self, name, email, age=0, roles=None):
        """Create a new user"""
        if roles is None:
            roles = []
        
        request = user_service_pb2.CreateUserRequest(
            name=name,
            email=email,
            age=age,
            roles=roles
        )
        
        try:
            response = self.stub.CreateUser(request)
            return response
        except grpc.RpcError as e:
            print(f"gRPC error: {e.code()} - {e.details()}")
            return None

    def close(self):
        """Close the gRPC channel"""
        self.channel.close()

# Usage Example
if __name__ == "__main__":
    # Start server in a separate process/thread
    # serve()
    
    # Client usage
    client = UserServiceClient()
    
    # Create user
    user = client.create_user("John Doe", "john@example.com", 30, ["admin", "user"])
    if user:
        print(f"Created user: {user.id} - {user.name}")
        
        # Get user
        retrieved_user = client.get_user(user.id)
        if retrieved_user:
            print(f"Retrieved user: {retrieved_user.name} - {retrieved_user.email}")
    
    # List all users
    users = client.list_users()
    print(f"Total users: {len(users)}")
    
    client.close()
```

WebSocket and modern API protocols enable efficient, real-time communication patterns that power contemporary web applications, from live chat and collaboration tools to high-performance distributed systems. Understanding these protocols is essential for building responsive, scalable applications.
