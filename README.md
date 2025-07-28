# Custom Modbus Scanner
### First ever attempt at programming in python (wahoo)
## Learning Objectives
- Understand how Modbus TCP protocol works (specifically function code 0x01 for reading coils
- How to craft and send raw Modbus packets using Python sockets
- how to parse the binary Modbus TCP response
- How to build a loop to scan a range of coil addresses

## Step 1: Understand the Modbus TCP Packet
Parts of a modbus TCP packet: 
 | Field | Size (Bytes) | Description |
|-------|--------------|-------------|
| Transaction ID | 2 | Used to match requests/responses |
| Protocol ID | 2 | Always 0 for Modbus TCP |
| Length | 2 | Remaining bytes in the message |
| Unit ID | 1 | Slave address (Usually 1) |
| Function Code | 1 | 0x01 to read coils | 
| Start Address | 2 | Where to start reading | 
| Quantity | 2 | Number of coils to read | 

## Step 2: Set up a simple TCP client
Use Python's socket module to send a TCP message to port 5020 to the PLC

### The socket module serves as a sort of walkie-talkie 
- you can dial a channel (IP + Port)
- you can send a message
- you can listen to incoming messages
- bidirectional communication

The socket module is often used for creating TCP clients or servers to send raw bytes over a network, binding to ports and listening for connections, and connecting to remote services by IP and port. 

### Socket module key concepts:
| Function | Purpose |
|----------|---------|
| socket() | create a socket (like creating a phone) |
| connect((host, port)) | connect to another machine (like dialing) |
| Send(data) | Send data to the remote machine |
| recv(buffer_size) | receive data from the other machine | 
| close() | Hang up the connection |

## socket() has two main arguments: 
**socket(family, type)**
### Family (Address Family)
Defines what type of addresses your socket will use

| Value | Meaning | Example | 
|-------|---------|---------|
| socket.AF_INET | IPv4 | Uses (192.168.1.150, 502) |
| socket.AF_INET6 | IPv6 | Uses (fe80::1ff:fe23:4567:890a, 502) |
| socket.AF_INET | Unix domain sockets (local IPC) | Only works on Unix/Linux systems |

### Type (Socket Type)
Defines how data is sent (TCP or UDP)

| value | Meaning | Example | 
|-------|---------|---------|
| socket.SOCK_STREAM | TCP | Reliable, connection oriented (used for Modbus TCP) |
| socket.SOCK_DGRAM | UDP | Fast, connectionless (used for things like DNS or SNMP) |
| socket.SOCK_RAW | Raw Socket | Used for packet crafting, sniffing, etc (requires root) |

## Step 3: Build a Raw Modbus Packet Request
Modbus TCP requests have two main sections: 
### 1) MBAP Header (7 bytes)
This is the Modbus Application Protocol header, and it is requried for TCP communication. 

| Field | Size (Bytes) | Description |
|-------|--------------|-------------|
| Transaction ID | 2 | Used to match requests/responses |
| Protocol ID | 2 | Always 0 for Modbus TCP |
| Length | 2 | Remaining bytes in the message |
| Unit ID | 1 | Slave address (Usually 1) |

### 2) PDU (Protocol Data Unit)
| Field | Size (Bytes) | Description |
|-------|--------------|-------------|
| Function Code | 1 | 0x01 to read coils | 
| Start Address | 2 | Where to start reading | 
| Quantity | 2 | Number of coils to read | 