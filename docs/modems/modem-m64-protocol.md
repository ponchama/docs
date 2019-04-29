## Modem-M64 serial protocol [DRAFT5]

This document describes the Water Linked Underwater Modem Link Layer protocol.

## Terminology

* Modem - Unit for transmitting/receiving data
* Packet - Unit of data transmitted together

## Version

This document describes protocol version 1.0 (major.minor)

The protocol versioning follows semantic versioning in that:

- MAJOR version increments represents incompatible API changes,
- MINOR version increments represents added functionality in a backwards-compatible manner

## Overview

The serial communication format is 112500 8-N-1 (no hardware flow control).

Packets sent to and received from the modem starts with 'W' and end with CR+LF.
The commands can be sent as a string or entered one char at a time from a terminal.
If the delay between bytes is more than ~3 seconds the packet will time out and a malformed request (W?) will be returned.

The protocol can support Water Linked modems with different payload sizes and other features.
To support any Water Linked modem the connection procedure is to:

- Get protocol version. Verify that the major version number is 1.
- Get payload size. Use this size when sending packets to the modem.

For Water Linked Modem-M64 the payload size is 8 bytes.

In order for two modems to communicate they must be configured to use different roles (A/B).


## Commands


| Command | Description | Response | Description |
|---------|-----------|-----------|-----------|
| Wv | Get protocol version | WV1.0 | Protocol version (major.minor)  |
| Wq | Get payload size | WQnnn | Where nnn is payload size: eg: WQ008 |
| Wa | Get modem settings | WCrc | Where r=role A/B and c=channel 0-7 eg: WCA0 |
| Wcxy | Set modem settings - Where x=role A/B and y=channel 0-7: eg: WcB7 | WCxy | Confirmed current settings eg: WCB7 |
| Wsnnnd...d | Send packet - Where nnn is number of bytes and d...d is payload eg: Ws008HelloSea | WAs / WNs | ACK/NAK |
| Wf | Flush tx buf  | WAf / WNf | ACK/NAK |
| Wl | Get tx buf len | WLnnnnn eg: WL107 | Current packets in buffer. |
| Wd | Status / diagnostic  | WD(link_status up/down),(packet_count),(packet_loss_count),(bit_error_rate in percent)| WDU,1234,17,3.5|
|  | Response when command cannot be understood | W? | Malformed request |
|  | Got a packet | WPnnnd...d | Got packet where nnn is number of bytes and d...d is payload eg: WP008HelloSea |


## Examples

Here is an example of setting up two modems and sending packets between them.

On top side modem (using role A):

| Command | Response | Description |
|---------|----------|-------------|
| Wv      | WV1.0    | Get protocol version |
| Wq      | WQ008    | Get payload size |
| WcA1    | WCA1     | Set role A and channel 1 |
| Wf      | WAf      | Flush transmit buffer (optional) |
| Ws008HelloSea | WAs  | Send packet |
|         |          | Wait for response |
|  | Ws008HelloTop   | Got response  |


On AUV/ROV modem (using role B):

| Command | Response | Description |
|---------|----------|-------------|
| Wv      | WV1.0    | Get protocol version |
| Wq      | WQ008    | Get payload size |
| WcB1    | WCB1     | Set role B and channel 1 |
|         |          | Wait for response |
|         | WP008HelloSea | Got packet |
| Ws008HelloTop  | WAs | Send response back  |
