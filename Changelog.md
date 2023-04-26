![Logo](/readme_images/logo_sm.jpg)
# Flexi-HAL CNC Controller

## Changelog

###A4 Revision

Initial Release

###A5 Revision
- Added HALT signal polarity selection.
- Modified RS485 UART isolation for better compatibility with more VFDs under LinuxCNC.
- Added jumper resistor option to route STP_ALARM signal to B limit switch input.
- Depopoulated isolation bypass headers to prevent magic smoke escape.
- Added termination resistors to encoder input to improve signal integrity.
- Added additional overvoltage protection to external circuits.
