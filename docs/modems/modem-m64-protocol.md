## Modem-M64 serial protocol

This document describes the Water Linked Underwater Modem Link Layer protocol.

## Terminology

* Modem - Unit for transmitting/receiving data
* Packet - Unit of data transmitted together
* ACK - Acknowledgement. The command issued was successful.
* NAK - Negative acknowledgement. The command issued failed.

## Version

This document describes protocol version 1,0 (major,minor)

The protocol versioning follows semantic versioning in that:

- MAJOR version increments represents incompatible API changes,
- MINOR version increments represents added functionality in a backwards-compatible manner

## Overview

The serial communication format is 112500 8-N-1 (no hardware flow control).

Packets sent to and received from the modem start with a `w` and end with end with LF or CR+LF. The packet format is:

| Start byte | Direction        | Command  | Options (0 to many)  | End byte       |
|------------|------------------|----------|----------------------|----------------|
| `w`        | `c` or `r`       | `x`      | `,[option]`          | `\n` or `\r\n` |

Direction is command (`c`) for commands issued to the modem and the modem replies with direction set to response (`r`).
The commands can be sent as a string or entered one char at a time from a terminal.

The protocol can support Water Linked modems with different payload sizes and extended features.
To support any Water Linked modem the connection procedure is to:

- Get protocol version. Verify that the major version number is 1.
- Get payload size. Use this size when queuing packets for transmission the modem.

For Water Linked Modem-M64 the payload size is 8 bytes.

In order for two modems to communicate they must be configured to use different roles (A/B) on the same channel.
The modem with role A will always transmit data packets.
The modem will role B will listen until it detects a packet from a modem of role A.
It will then start transmitting responses back to the modem with role A.
The modem with role B will go back to listen mode if several consecutive packets from the modem with role A cannot be decoded (ie signal is lost).


## Commands

| Command | Description | Response | Description |
|---------|-------------|----------|-------------|
| `wcv`   | Get protocol version | `wrv,`*[major],[minor]* | Protocol version. eg: `wrv,1,0` |
| `wcn`   | Get payload size | `wrn,`*[size]* | Where size is supported payload size: eg: `wrn,8` |
| `wcc`   | Get modem configuration | `wrc,`*[role],[channel]* | Where role=a or b and channel=1-7 eg: `wrc,a,4` |
| `wcs,`*[role],[channel]* | Set modem configuration - Where role=a or b and channel 1-7: eg: `wrs,b,4` | `wrs,a` or `wrs,n` | ACK or NAK |
| `wcl`   | Get transmit queue length | `wrl,`*[q]* | Number of packets currently queued for transmission. eg: `wrl,107` |
| `wcf`   | Flush transmit queue  | `wrf,a` or `wrf,n` | ACK or NAK |
| `wcd`   | Get diagnostic  | `wrd,`*[link],[packet_count],[packet_loss_count],[bit_error_rate]* | link=y if connection with other modem, otherwise n.  eg: `wrd,y,1234,17,3.5`|
|         |             |          |              |
| `wcq,`*[size],[payload]* | Queue packet for transmission. Payload can be binary. eg: `wcq,8,HelloSea` | `wrq,a` or `wrq,n` | ACK or NAK |
|         |             | `wrp,`*[size],[payload]* | Got packet from other modem eg: `wrp,8,Welcome!` |
|         |             | `wr?` | Malformed request: Response when command cannot be understood |


## Examples

Here is an example of setting up two modems and sending packets between them.

On top side modem (using role A):

| Command          | Response         | Description |
|------------------|------------------|-------------|
| `wcv`            | `wrv,1,0`        | Get protocol version |
| `wcn`            | `wrn,8`          | Get payload size |
| `wcc,a,4`        | `wrc,a,4`        | Set role A and channel 4 |
| `wcf`            | `wrf,a`          | Flush transmit buffer (optional) |
| `wcq,8,HelloSea` | `wrq,a`          | Send packet |
|                  |                  | Wait for response |
|                  | `wrp,8,HelloTop` | Got response  |

On AUV/ROV modem (using role B):

| Command          | Response         | Description |
|------------------|------------------|-------------|
| `wcv`            | `wrv,1,0`        | Get protocol version |
| `wcn`            | `wrn,8`          | Get payload size |
| `wcc,b,4`        | `wrc,b,4`        | Set role B and channel 4 |
|                  |                  | Wait for response |
|                  | `wrp,8,HelloSea` | Got packet |
| `wcq,8,HelloTop` | `wrq,a`          | Send response back  |
