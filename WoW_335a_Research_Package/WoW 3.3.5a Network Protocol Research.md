# WoW 3.3.5a Network Protocol Research

## World Packet Structure

World packets are the primary method of communication between the client and the world server.

### Server Message (SMSG) Header

|Offset |Size / Endianness |Type |Name |Description |
|---|---|---|---|---|
|0x0 |2 / Big |uint16 |size |Size of the packet including the opcode field. |
|0x2 |2 / Little |uint16 |opcode |Opcode for the packet. |

### Client Message (CMSG) Header

|Offset |Size / Endianness |Type |Name |Description |
|---|---|---|---|---|
|0x0 |2 / Big |uint16 |size |Size of the packet including the opcode field. |
|0x2 |4 / Little |uint32 |opcode |Opcode for the packet. |

### Authentication Process

1. The server sends a `SMSG_AUTH_CHALLENGE`.
2. The client sends a `CMSG_AUTH_SESSION`.
3. The server sends a `SMSG_AUTH_RESPONSE`.
4. The client requests a character screen with `CMSG_CHAR_ENUM`.
5. The server responds with a list of characters in a `SMSG_CHAR_ENUM`.
6. The client is now at the character screen.

## Login Packet Structure

Login packets are used for authentication and realm selection.

### Login Opcodes

|Name |Value |Description |
|---|---|---|
|CMD_AUTH_LOGON_CHALLENGE |0x00 |Intial information sent by client and then challenge by server. |
|CMD_AUTH_LOGON_PROOF |0x01 |Proof sent by client and then server. |
|CMD_AUTH_RECONNECT_CHALLENGE |0x02 |Reconnect challenge sent by client and then server. |
|CMD_AUTH_RECONNECT_PROOF |0x03 |Reconnect proof sent by client and then server. |
|CMD_SURVEY_RESULT |0x04 |Used for hardware survey. |
|CMD_REALM_LIST |0x10 |Realmlist request sent by client and realmlist information sent by server. |
|CMD_XFER_INITIATE |0x30 |Sent by server when it wants to send data. |
|CMD_XFER_DATA |0x31 |Data sent by the server. |
|CMD_XFER_ACCEPT |0x32 |Sent by client after getting an CMD_XFER_INITIATE from the server. |
|CMD_XFER_RESUME |0x33 |Sent by client that already has part of the data the server wants to send. |
|CMD_XFER_CANCEL |0x34 |Sent by client that wishes to gracefully close the connection. |

### Login Process

1. Client sends a `CMD_AUTH_LOGON_CHALLENGE_Client` containing the username and version information.
2. Server responds with a `CMD_AUTH_LOGON_CHALLENGE_Server` containing either authentication values or a failure code.
3. Client performs authentication calculations and sends a `CMD_AUTH_LOGON_PROOF_Client`.
4. Server performs calculations and returns a `CMD_AUTH_LOGON_PROOF_Server` with either a server proof or a failure code.
5. Client requests a realm list with `CMD_REALM_LIST_Client` and the server responds with `CMD_REALM_LIST_Server`.
6. Client selects a server and opens a TCP connection to the Realm Server.
7. Realm Server sends the `SMSG_AUTH_CHALLENGE` World Packet to the client and continues with realm server authentication.
