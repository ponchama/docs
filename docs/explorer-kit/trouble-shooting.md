# Trouble shooting

If you are experiencing issues with the Underwater GPS look at the following points to help you trouble shoot.

## Network setup

* Are you able to see the Underwater GPS GUI?
* What is the setting of the DIP switches for configuring network?
* If the DIP switches are set to a static IP: Have you configured a static IP for your computer?
* Are you connecting directly to the Master-D1 or is your computer on the same network?

## Generic

* Are you running the latest software version?
* Are there any warnings displayed in the GUI?

## Receiver setup

* Are the receivers connected to the correct ports (numbered 1-4) on the peli case?
* Have you measured the distances to the recivers correctly and are the distances matching the measurements in the recieiver setup of the GUI?
* Is the search range matching the operational area?
* If receivers are on a line, have you limited the search area to 1 side of the "line"?
* Try lift receiver 1 out of the water and verify that receiver 1 has a flat signal in the diagnostic view. Repeat for the other receivers. This helps verify that you have connected correctly.

## Settings

* Have you selected the correct channel when using Locator-U1 or Locator-S1?
* If you are statinary: Have you selected a static position for the GPS and IMU?
* If you are moving: Have you selected on-board (or external) GPS and IMU?

## Diagnostic page

* Is there signal from all of the receivers?
* Is there a significant peek in the signal for each receiver?
