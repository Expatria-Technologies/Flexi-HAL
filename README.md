# GRBLHAL2000 for PrintNC EST

![Logo](/readme_images/logo_sm.jpg)

Expatria Technologies GRBLHAL Breakout board

grblHAL is a no-compromise, high performance, low cost alternative to parallel-port-based motion control for CNC milling, and the GRBLHAL2000 breakout board is a highly integrated, no compromise host platform for this awesome software.  Utilizing the high-performance PJRC Teensy 4.1 module, this board is designed to be a community driven project for the PrintNC, but useful for driving any type of CNC machine that uses external stepper drivers.

The GRBLHAL2000 was designed specifically for that machines that we are building in our lab, it is not intended to be everything to everyone.  The GRBL world is surprisingly vibrant and it is very likely that if we are missing a key feature for you, there are other designs out there that will be just what you are looking for.  But this board does include a few features that we couldn't find on other boards, and it reduces the amount of extra wiring in our setups.  In the co-operative spirit of the PrintNC community, and Open Source Hardware, the PNC HAL 2000 will be licensed and free to use by all parties, including commercial parties, under the CERN-OHL-P V2 license.  It is our hope that the community finds the design useful and that it may be carried forward to help advance the PrintNC and broader CNC hobby community.

Since this is not a commercial product, the best place to get a board (other than ordering a stack from the provided CAM files) is from the PrintNC community:
https://wiki.printnc.info/en/grbl/GRBLHAL2000-marketplace

The most recent A6 revision incorporates community driven updates from the PrintNC Electronic Standardization (EST) Project.  As part of this project, two additional breakout boards have been created for the user controls and limits/probe inputs.  These are simple boards and could easily be milled and hand assembled, but fabrication files for each are available in the CAM_Outputs folder.  In addition, the GRBLHAL2000 is intended to be used with the I2C jog controller or similar peripheral:

https://github.com/Expatria-Technologies/I2C_Jog_Controller


The key features of the GRBLHAL2000:

1) Integrated support for 3 wire powered switches such as the inductive type commonly used by the PrintNC community.
3) Onboard 5V regulator for the Teensy and PI connectors.
4) Integrated RS485 with automatic direction control.
5) Support for closed loop stepper motors and servos.
6) Differential interface for Spindle Sync inputs.
7) Sparkfun QWIIC differential I2C endpoint - extends the range of I2C and creates a robust data link for real-time jogging and control.  Note that due to chip availability issues, this interface has been replaced with pin headers on the A5 revision onwards.  See below for details.
8) Raspberry Pi GPIO connector allows integration of sender software and extended possibilities for network connectivity.

In addition, the board offers many of the same features found on other 32 bit GRBL controllers:

1) 5 axes of external step/direction for driving external stepper or servo drivers.
2) 10V or 5V spindle control.
3) 3 wire (powered) connections for standard GRBL buttons on breakout RJ45 connector.
4) XYZA limit switches and probe/toolsetter on breakout RJ45 connector.
5) Flood/mist relay drivers
6) Additional auxilliary inputs and relay driver outputs.

Optimized GRBLHAL driver is located here:
https://github.com/Expatria-Technologies/iMXRT1062

## GRBLHAL2000 Overview

![Overview Image](/readme_images/Board_Overview.jpg)

### Teensy 4.1 Module
The GRBLHAL2000 uses the excellent Teensy4.1 port of GRBLHAL.  Both ethernet and USB-UART solutions can be used for sending g-code.  A UART is also connected to the PI header.

#### Soldering Instructions
The following pins need to be soldered between the GrblHAL2k and the Teensy: 
<img src="/readme_images/TeensySoldering.png" width="500">

https://github.com/grblHAL/Plugin_networking/

### Power Input
There is a single input for 12-24VDC.  The board has its own onboard 5V regulator to power the Teensy module as well as a Pi Zero W (recommended) or Pi 3 A+ that is attached to the Pi GPIO header.  There is also a small capacity 12V LDO that is specifically for driving the limit and user switches, as well as optionally providing the base voltage for the 10V spindle output.  When driving the board with less than 14V input, it may not be possible to adjust the spindle output voltage to the full 10V.  In this case we recommend applying 12V to the external spindle supply input directly.

It is recommended that you cut the 5V power distribution trace on the Teensy 4.1.  This ensures that the USB power on the Teensy and the 5V regulator on the GRBLHAL2000 do not interact and create potential issues with back-powering.  Alternative solutions are detailed at pjrc:

https://www.pjrc.com/teensy/external_power.html

GRBLHAL2000 has reverse polarity as well as over-current protection beyond 1A.  This is important to consider when using external relays that draw a lot of current as this may overwhelm the capacity of the board.  If you need to drive more than 250 mA through the auxillary and mist/coolant relay outputs, external relays are likely required.

### Stepper Drivers
<img src="/readme_images/Stepper_Pins.jpg" width="300">

The stepper drivers are designed to be used with IDC connectors that are quick to assemble.  Unfortuantely you will need to ensure that at the external driver the high and low signal pairs are connected correctly as there is no standard pinout on these drivers.  The 8 pin conneciton allows you to run a high and low pair for every signal to ensure the best possible signal integrity.  If desired, L2 and L3 can be swapped so that the steppers can be driven with 12V.

### Analog Spindle Control
Traditional GRBL spindle control interface for 0-10V or 0-5V spindle control.  Uses a dual-stage output driver for linear response.  Spindle power supply is selectable between 5V, 12V or external.  Note that when running the board with less than 14v input, it may not be possible to reach the full 10V output level - this is due to the dropout of the 12V LDO.  In this case, use the external spindle power input to connect your 12v and bypass the LDO for the spindle control voltage.

On the bottom side of the Hal2k there is a solder bridge near the spindle section labelled PWM_CUT.  This jumper allows you to have a 12V compliant TTL PWM signal to drive a device like a laser engraver or an ESC.  Open the jumper for PWM output and close the jumper to output a continuous 0-10V analog signal.


### RS485 Spindle Control
This interface is primarily intended to be used with a Huanyang style VFD for spindle control.  The A and B pins are marked on the bottoms side of the PCB.  Simply connect the appropriate pins to the terminals on the VFD.  The auto-direction sensing circuit is modified from Bryan Varner's project:

https://github.com/bvarner/pi485

https://github.com/grblHAL/Plugins_spindle/

### 5 Axis limit inputs

<img src="/readme_images/limit_mod_render.jpg" width="150">

By default both GRBL and the GRBLHAL2000 expect NPN NC limit switches.  PNP switches are not supported. NO switches can also be used on any switch input

The first four axes have single limit inputs that are accessed via the RJ45 limit breakout connector.  A sample design for a breakout panel is included in the CAM_Outputs folder.  The fifth (M4 or B axis) limit input is multiplexed via XOR logic between two 3 wire connections to allow for future flexibility.  GRBL always knows the direction of travel so individual min and max pins are not required.  Auto-squaring is supported by enabling ganged axes in GRBLHAL and setting the appropriate pins.

In addition to the B limit, there are two probe input pins on the limit RJ45 breakout connector that are also multiplexed via XOR logic.

For both of the dual-input signals there is no need to terminate unused ports.

The RJ45 pinout:

<img src="/readme_images/limit_rj45_pinout.jpg" width="150">

### User Buttons
<img src="/readme_images/User_mod_render.jpg" width="400">

Standard GRBL functions are mapped to 4 inputs.  These signals are primarily intended to be used via the user RJ45 connector.  For convenience, the HALT and DOOR signals are also exposed via 3 wire connections on the main PCB.  When multiplexed these signals must be NO logic.  A sample design for a button panel utilizing clear PETG buttons is included in the CAM_Outputs folder.

The RJ45 pinout:

<img src="/readme_images/user_rj45_pinout.jpg" width="150">

### Flood and Mist relay drivers - Auxillary relay drivers
The relay voltage is selectable between either the 12-24V input voltage, or the onboard 5V supply. P9 allows you to select the relay voltage.  If you need to drive more than 250 mA through the auxillary and mist/coolant relay outputs, larger external relays are likley required.

### Real-Time Control Port

<img src="/readme_images/qwiic-logo-registered.jpg" width="100">

On the A5 revision, this interface was revised to remove design dependence on the PCA9615 I2C extender chip.  Instead, pin headers are populated that expose all of the necessary signals to allow a custom I2C extender implementation.  In addition, a reference implementation using the PCA9615 is provided in the QWIIC_CARD folder to see how this might be accomplished and to allow interoperation with existing QWIIC pendants.

Please note that at least on Expatria designed pendants, the I2C interface module is usually included as a breakaway tab as part of the jogger pendant.  It will not be necessary to build the example QWIIC card separately.

This port is intended to allow for external pendant type devices to issue real-time jogging and override controls to the GRBLHAL controller.  It follows the QWIIC interface from Sparkfun, but adds additional signals for the keypad interrupt as well as the emergency stop.  We feel that a robust and wired control is the safest way to interact with a CNC machine in real time.  A simple reference controller implementation is under development, but there are some code examples referenced in the GRBLHAL I2C keypad plugin repository:

https://github.com/grblHAL/Plugin_I2C_keypad/

https://www.sparkfun.com/qwiic

### Spindle Sync Port
This port allows a differential connection to an external module for a robust GRBLHAL lathe implementation.  A reference encoder design is under development.  In addition solder jumper options are present to allow this connector to provide a quadrature encoder signal to the Teensy 4.1 QEI pins for future development.

### AUX Relay Port
Four axilliary relay outputs are exposed.  These have a maximum combined drive current of 500 mA.  The relay voltage can be selected via a 3 pin jumper between the external voltage input (12-28V) and the onboard 5V supply.

### Raspberry PI expansion header

This is a standard Raspberry PI GPIO header.  The Pi has the ability to drive the axuilliary outputs, as well as read the status of the real-time control signals.  Three Pi GPIO signals are also brought out to a header on the top side of the board.  The GRBLHAL2000 is capable of powering a Pi Zero W or PI 3 A+ directly, but it cannot supply enough current for a full Pi 3 or Pi4 - when using those you must supply power to the PI externally.  Please note that A6 revision PCBA require a 90 degree headerwith the Pi mounted in a vertical orientation.

For communicating with the Teensy, the Pi can act as a standard g-code sender and send data over the connected UART.  The I2C keypad interface is also connected to allow for the potential to develop pi based real-time controls.  Finally, the Pi can assert the user switch controls such as emergency stop.  GRBL auxillary inputs are also routed to the Pi in the event they are unused by the GRBLHAL application.  The Pi can also be used to drive the auxilliar relay outputs.

### GRBLHAL2000 EST Ecosystem
Complete GRBLHAL2000 ecosystem with Jogger, breakouts and motion controller.

<img src="/readme_images/EST_Ecosystem.png" width="500">

### Attributions
This project uses components from the very helpful actiBMS library for JLCPCB SMT parts.

https://github.com/actiBMS/JLCSMT_LIB



