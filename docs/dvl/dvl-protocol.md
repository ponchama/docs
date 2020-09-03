## Water Linked DVL protocol

This document describes the Water Linked DVL protocols (serial and ethernet).

## Terminology

* DVL - Doppler Velocity Log - Hydro-acoustic unit which uses acoustic beams to measure distance to bottom surface and the velocity which the unit is moving across the surface.
* ACK - Acknowledgement. The command issued was successful.
* NAK - Negative acknowledgement. The command issued failed.

## Version

This document describes protocol version 2.0.x (major.minor.patch)

The protocol versioning follows semantic versioning in that:

- MAJOR version increments represent incompatible API changes
- MINOR version increments represent added functionality in a backwards-compatible manner
- PATCH version increments represent backwards-compatible bug fixes

## Serial Protocol

### Overview

The serial communication format is 115200 8-N-1 (no hardware flow control).

Packets sent to and received from the DVL start with a `w` and end with LF or CR+LF. The packet format is:

| Start byte | Direction        | Command  | Options (0 to many)  | Checksum | End byte       |
|------------|------------------|----------|----------------------|----------|----------------|
| `w`        | `c` or `r`       | `x`      | `,[option]`          | `*xx`    | `\n` or `\r\n` |

Direction is command (`c`) for commands issued to the DVL and the DVL replies with direction set to response (`r`).
The commands can be sent as a string or entered one char at a time from a terminal.

The protocol can support Water Linked DVLs with different feature sets.
To support any Water Linked DVL the connection procedure is to:

- Get protocol version. Verify that the major version number is 2.
- Get product detail. Verify product type is dvl.

!!!note
    Checksum is optional when sending commands to the DVL. The DVL always returns a checksum. The checksum algorithm
    is CRC-8 and it is formatted as a hexadecimal number using 2 lower-case charaters (ex: `*c3`).

### Commands

Commands in the table are shown **without** the checksum for readability.

| Command | Description | Response | Description |
|---------|-------------|----------|-------------|
| `wcv`   | Get protocol version | `wrv,`*[major],[minor],[patch]* | Protocol version. eg: `wrv,2.0.0` |
| `wcw`   | Get product detail | `wrw,`*[type]*,*[name]*,*[version]*,*[chipID]*,*[IP address]* | Where type is dvl, name is product name, version is software version, chip ID is the chip ID and IP address is the IP if connected to DHCP server: eg: `wrw,dvl,dvl-a50,1.3.0,0xdeadbeef,10.11.12.95` |
| `wcc`   | Get configuration | TBD | Get configuration |
| `wcs,`*[TBD]*   | Set configuration | TBD | Set configuration |
|         |             |          |              |
|         |             | `wrx,`*[details below]* | Velocities measured. See details below |
|         |             | `wr?` | Malformed request: Response when packet cannot be understood |
|         |             | `wr!` | Malformed request: Packet does not match the given checksum |

### Velocity report

Velocity report is outputted after each measurement has been completed. The expected update rate varies depending on the altitude and will be in the range is from 2-10 Hz. The X, Y, Z axis are oriented according to the marking on the DVL.

The velocities measured response is on the following format:
`wrx,`*[time],[vx],[vy],[vz],[fom],[altitude],[valid]*

| Variable | Description |
|----------|-------------|
| time | Milliseconds since last velocity report (ms) |
| vx | Measured velocity in x direction (m/s) |
| vy | Measured velocity in y direction (m/s) |
| vz | Measured velocity in z direction (m/s) |
| fom | Figure of merit, a measure of the accuracy of the measured velocities  (m/s) |
| altitude | Measured altitude to the bottom (m) |
| valid | If valid is "y" the DVL has lock on the bottom and the altitude and velocities are valid (y/n) |

Example:
`wrx,125,0.05,0.01,0.001,0.5,0.1,y`

<!--
## Velocity report type B

`wrx,`*[time],[vx],[vy],[vz],[distance],[std],[rssi],[nsd]*

| Variable | Description |
|----------|-------------|
| time | Milliseconds since last velocity report |
| vx | Measured velocity in x direction (m/s) |
| vy | Measured velocity in y direction (m/s) |
| vz | Measured velocity in z direction (m/s) |
| std | Standard deviation (m/s), a measure of the accuracy of the measured velocities |
| distance | Measured distance to the bottom (altitude) |
| rssi | Receiver signal strength indication, indication of signal strength (dB) |
| nsd | Noise spectral density, indication of background noise (dB) |

## Examples

Here is an example of setting up a DVLs and receiving velocities.

On top side DVL (using role A):

| Command          | Response            | Description |
|------------------|---------------------|-------------|
| `wcv`            | `wrv,1,0,1*44`      | Get protocol version |
| `wcw`            | `wrw,xxxxx*ba`      | Get product detail |
|                  |                     | Wait for measurements from DVL |
|                  | `wrx,xxxxxx*bb`     | Got measurements  |
-->


## Ethernet protocol (TCP)

### Overview

The DVL supports sending velocity updates using the Transmission Control Protocol (TCP). The DVL runs a TCP server on port 16171.

Each packet sent contains a velocity report from the DVL on JSON format.

### Velocity report

Velocity report is outputted after each measurement has been completed. The expected update rate varies depending on the altitude. The X, Y, Z axis are oriented according to the DVL.

| Variable | Description |
|----------|-------------|
| time | Milliseconds since last velocity report (ms) |
| vx | Measured velocity in x direction (m/s) |
| vy | Measured velocity in y direction (m/s) |
| vz | Measured velocity in z direction (m/s) |
| fom | Figure of merit, a measure of the accuracy of the measured velocities  (m/s) |
| altitude | Measured altitude to the bottom  (m) |
| velocity_valid | If valid is true the DVL has lock on the bottom and the altitude and velocities are valid (true/false) |
| status | Reports if there are any issues with the DVL. 0 means no errors |
| format | Format type and version for the velocity report |
| transducers | Is a list containing information from each transducer: [id, velocity, distance, rssi, nsd, beam_valid] |

Example of TCP report. (indented for readability)

```
{
  "time": 170.52674865722656,
  "vx": -0.00563613697886467,
  "vy": -0.007631152402609587,
  "vz": -0.007641898933798075,
  "fom": 0.001959984190762043,
  "altitude": 0.6173566579818726,
  "transducers": [
    {
      "id": 0,
      "velocity": -0.007625679485499859,
      "distance": 0.6769760251045227,
      "rssi": 38.66838836669922,
      "nsd": 18.295578002929688,
      "beam_valid": true
    },
    {
      "id": 1,
      "velocity": -0.0034413286484777927,
      "distance": 0.6769760251045227,
      "rssi": 35.403541564941406,
      "nsd": 19.518909454345703,
      "beam_valid": true
    },
    {
      "id": 2,
      "velocity": -0.006717036943882704,
      "distance": 0.6653040051460266,
      "rssi": 41.03888702392578,
      "nsd": 20.25017738342285,
      "beam_valid": true
    },
    {
      "id": 3,
      "velocity": -0.01045388076454401,
      "distance": 0.6536320447921753,
      "rssi": 31.09071922302246,
      "nsd": 17.366933822631836,
      "beam_valid": true
    }
  ],
  "velocity_valid": true,
  "status": 0,
  "format": "json_v1"
}

```
