# DVL-A50

## Description
The DVL A50 is – by far – the world’s smallest commercially available Doppler Velocity Log.

The A50 is establishing a new market standard with its high performance, ultra-small 4 beam setup, open interface protocol and low cost.

The DVL estimates velocity relative to the sea bottom by sending acoustic waves from the four angled transducers and then measure the frequency shift (doppler effect) from the received echo. By combining the measurements of all four transducers and the time between each acoustic pulse, it is possible to very accurately estimate the speed and direction of movement.

## Key specifications

* 0.05 - 50 meter range
* 1 MHz transducer frequency
* 4-10 Hz ping rate (altitude dependent)
* 2 m/s max velocity
* ± 0.1 cm/s long term accuracy
* 1000 meter depth rating
* 10-30 V input voltage
* Ethernet and UART

## LED Signals

* No green light: Power is off.

* Flashing green light (slow): DVL loocking for bottom lock.

* Fixed green light: DVL has bottom lock. The LED is mostly on and blinks quickly to show we are alive.

## Wiring interface

The tables below shows the pinning of the DVL-A50 interface.

| Interface           | Color |
| :------------------ | :-- |
| Positive (10-18V) | Red  |
| Negative/Ground | Black   |
| ETH TX+ | Orange/White   |
| ETH TX- | Orange   |
| ETH RX+ | Green/White  |
| ETH RX- | Green  |
| UART TX | Brown/White   |
| UART RX | Brown  |

## Terminal Interface

The DVL-A50 has a 3.3 volt UART interface.

| Settings           | Value |
| :------------------ | :-- |
| Baud rate | 115200  |
| Data parity stop | 8N1   |
| Flow control | None  |

<!--
## Libraries and code examples

Example code and libraries that can be used to communicate with the DVL on the terminal interface:

* [Python](https://github.com/waterlinked/dvl-python)
 -->

## Axis convensions

The axis on the DVL-A50 are oriented North-East-Down (NED)

* X axis is pointing forward (LED is forward, cable backward)
* Y axis is pointing right
* Z axis is pointing down (mounting holes are up, transducers are down)

## Dimensions

![dvl_a50_dimensions](../img/dvl_dimensions.png)


## Mounting Holes

![dvl_a50_mounting_holes_drawing](../img/dvl_mounting_holes_drawing.png)
