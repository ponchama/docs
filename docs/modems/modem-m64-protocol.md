## Modem-M64 serial protocol [DRAFT6]

This document describes the Water Linked Underwater Modem Link Layer protocol.

## Terminology

* Modem - Unit for transmitting/receiving data
* Packet - Unit of data transmitted together
* ACK - Acknowledgement. The command issued was successful.
* NAK - Negative acknowledgement. The command issued failed.

## Version

This document describes protocol version 1.0 (major.minor)

The protocol versioning follows semantic versioning in that:

- MAJOR version increments represents incompatible API changes,
- MINOR version increments represents added functionality in a backwards-compatible manner

## Overview

The serial communication format is 112500 8-N-1 (no hardware flow control).

Packets sent to and received from the modem starts with 'W' and end with LF or CR+LF.
The commands can be sent as a string or entered one char at a time from a terminal.
If the delay between bytes is more than ~2 seconds the packet will time out and a malformed request (W?) will be returned.

The protocol can support Water Linked modems with different payload sizes and other features.
To support any Water Linked modem the connection procedure is to:

- Get protocol version. Verify that the major version number is 1.
- Get payload size. Use this size when sending packets to the modem.

For Water Linked Modem-M64 the payload size is 8 bytes.

In order for two modems to communicate they must be configured to use different roles (A/B) on the same channel.
The modem with role A will always transmit data packets.
The modem will role B will listen until it detects a packet from a modem of role A.
It will then start transmitting responses back to the modem with role A.
The modem with role B will go back to listen mode if several consecutive packets from the modem with role A cannot be decoded (ie signal is lost).


## Commands


| Command | Description | Response | Description |
|---------|-----------|-----------|-----------|
| Wv | Get protocol version | WV1.0 | Protocol version (major.minor)  |
| Wq | Get payload size | WQn | Where n is payload size: eg: WQ8 |
| Wa | Get modem settings | WCr,c | Where r=role A/B and c=channel 0-7 eg: WCA,0 |
| Wcx,y | Set modem settings - Where x=role A/B and y=channel 0-7: eg: WcB,7 | WCx,y | Confirmed current settings eg: WCB,7 |
| Wsn,d...d | Send packet - Where n is number of bytes and d...d is payload eg: Ws8,HelloSea | WAs / WNs | Payload can be binary. Response: ACK/NAK |
| Wf | Flush tx buf  | WAf / WNf | ACK/NAK |
| Wl | Get tx buf len | WLnnnnn eg: WL107 | Current packets in buffer. |
| Wd | Status / diagnostic  | WD(link_status up/down),(packet_count),(packet_loss_count),(bit_error_rate in percent)| WDU,1234,17,3.5|
|  | Response when command cannot be understood | W? | Malformed request |
|  | Got a packet | WPn,d...d | Got packet where n is number of bytes and d...d is payload eg: WP8,Welcome! |


## Examples

Here is an example of setting up two modems and sending packets between them.

On top side modem (using role A):

| Command | Response | Description |
|---------|----------|-------------|
| Wv      | WV1.0    | Get protocol version |
| Wq      | WQ8      | Get payload size |
| WcA,1   | WCA,1    | Set role A and channel 1 |
| Wf      | WAf      | Flush transmit buffer (optional) |
| Ws8,HelloSea | WAs | Send packet |
|         |          | Wait for response |
|  | WP8,HelloTop    | Got response  |


On AUV/ROV modem (using role B):

| Command | Response | Description |
|---------|----------|-------------|
| Wv      | WV1.0    | Get protocol version |
| Wq      | WQ8      | Get payload size |
| WcB,1   | WCB,1    | Set role B and channel 1 |
|         |          | Wait for response |
|         | WP8,HelloSea | Got packet |
| Ws8,HelloTop | WAs | Send response back  |
