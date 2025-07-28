# Custom Modbus Scanner
### First ever attempt at programming in python
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

