# Flexi-HAL CNC Controller

![Logo](/readme_images/logo_sm.jpg)

Expatria Technologies GRBLHAL and LinuxCNC (and more!) CNC control board

The Flexi-HAL was designed to be an EMI resistant IO platform for any microcontroller based CNC/motion control firmware or software.  But this board does include a few features that we couldn't find on other boards, and it reduces the amount of extra wiring in our setups.  In the co-operative spirit of the PrintNC and other CNC communities, and Open Source Hardware, the Flexi-HAL will be licensed and free to use by all parties, including commercial parties, under the CERN-OHL-S V2 license.  It is our hope that the community finds the design useful and that it may be carried forward to help advance the PrintNC and broader CNC hobby community.

The best place to get a board (other than ordering a stack from the provided CAM files) is from Expatria online shop **LINK***.  If you like our designs, please consider buying a board to help support our continued open-source development efforts.  It's the best way to support what we're doing.

The Flexi-HAL incorporates community driven elements from the PrintNC Electronic Standardization (EST) Project.  As part of this project, two additional breakout boards have been created for the user controls and limits/probe inputs.  These are simple boards and could easily be milled and hand assembled, but fabrication files for each are available in the CAM_Outputs folder.  The inputs are accessible via the RJ45 connectors on the Flexi-HAL mainboard.  In addition, the Flexi-HAL is intended to be used with the Expatria Real-Time jog controller or similar peripheral:

https://github.com/Expatria-Technologies/RT_Jog_Controller


<img src="/readme_images/Board_Photo.jpg" width="700">


The key features of the Flexi-HAL:

1) Integrated support for 3 wire powered switches such as the inductive type commonly used by the PrintNC community.
2) 5 Axis of isolated step/dir motor control featuring high-speed digital isolators and differential signal drivers for maximum signal integrity and step rates.
3) Onboard 5V regulator for the MCU, pendant and PI connectors.
4) Integrated RS485 with automatic direction control.
5) Support for closed loop stepper motors and servos.
6) Differential interface for a spindle encoder input that can be utilized by GRBLHAL and LinuxCNC.
7) Expatria Technologies real-time control port for remote handwheels and pendants.
8) Raspberry Pi GPIO connector allows integration of sender software allows the board to host a full LinuxCNC installation.

In addition, the board offers many of the same features found on other 32 bit controllers:

1) 10V or 5V spindle control.
3) 3 wire (powered) connections for standard CNC buttons on a breakout RJ45 connector that utilizes components compatible with the PrintNC EST project.
4) XYZA limit switches and probe/toolsetter on breakout RJ45 connector.
5) Flood/mist relay drivers
6) Additional auxilliary inputs and relay driver outputs.

Optimized GRBLHAL driver is located here:
https://github.com/Expatria-Technologies/STM32F4xx

LinuxCNC build for SPI Remora will be published when it is available.

## Flexi-HAL Overview

<img src="/readme_images/Board_Overview.png" width="700">

### STM32F446 Microcontroller

Primarily, the board supports both GRBLHAL and LinuxCNC.  Binary firmware builds for different axis configurations are published on the Expatria Github.  In addition, a customized port of the awesome Remora project has been developed alongside Flexi-HAL so that you can easily switch to LinuxCNC.  With LinuxCNC, a Raspberry Pi 4b is installed in the Pi GPIO header to ensure the best possible signal integrity for the SPI step generation interface.

![Overview Image](/readme_images/Pi_Installed.png)

### Power Input
There is a single input for 12-24VDC.  The board has its own onboard 5V regulator to power the Teensy module as well as a Pi Zero W (recommended) or Pi 3 A+ that is attached to the Pi GPIO header.  There is also a small capacity 12V LDO that is specifically for driving the limit and user switches, as well as optionally providing the base voltage for the 10V spindle output.  When driving the board with less than 14V input, it may not be possible to adjust the spindle output voltage to the full 10V.  In this case we recommend applying 12V to the external spindle supply input directly.

It is recommended that you cut the 5V power distribution trace on the Teensy 4.1.  This ensures that the USB power on the Teensy and the 5V regulator on the Flexi-HAL do not interact and create potential issues with back-powering.  Alternative solutions are detailed at pjrc:

https://www.pjrc.com/teensy/external_power.html

Flexi-HAL has reverse polarity as well as over-current protection beyond 1A.  This is important to consider when using external relays that draw a lot of current as this may overwhelm the capacity of the board.  If you need to drive more than 250 mA through the auxillary and mist/coolant relay outputs, external relays are likely required.

### Stepper Drivers
<img src="/readme_images/Stepper_Pins.jpg" width="300">

The stepper drivers are designed to be used with IDC connectors that are quick to assemble.  Unfortuantely you will need to ensure that at the external driver the high and low signal pairs are connected correctly as there is no standard pinout on these drivers.  The 8 pin conneciton allows you to run a high and low pair for every signal to ensure the best possible signal integrity.  The Flexi-HAL uses high speed digital isolators and differential RS-422 style signal drivers for maximum speed and signal integrity.

### Analog Spindle Control

Traditional GRBL spindle control interface for 0-10V or 0-5V spindle control.  Uses a dual-stage output driver for linear response.  Spindle power supply is selectable between 5V, 12V or external.  Note that when running the board with less than 14v input, it may not be possible to reach the full 10V output level - this is due to the dropout of the 12V LDO.  In this case, use the external spindle power input to connect your 12v and bypass the LDO for the spindle control voltage.

<img src="/readme_images/Spindle_PWM_Config.jpg" width="300">

Near the main power input of the Flexi-HAL there is a diagram showing how a set of jumpers may be configured to enable 0-10V analog or TTL PWM output.  This jumper allows you to have a 12V compliant TTL PWM signal to drive a device like a laser engraver or an ESC.


### RS485 Spindle Control
This interface is primarily intended to be used with a Huanyang style VFD for spindle control.  The A and B pins are marked on the bottoms side of the PCB.  Simply connect the appropriate pins to the terminals on the VFD.  Note that the GND pin should be used for signal common, it should not be connected to the shield of a shielded cable.

https://github.com/grblHAL/Plugins_spindle/

### 5 Axis limit inputs

<img src="/readme_images/limit_mod_render.jpg" width="150">

By default both GRBL and the Flexi-HAL expect NPN NC limit switches.  PNP switches are not supported. NO switches can also be used on any switch input.

The first four axes have single limit inputs that are also accessible via the RJ45 EST limit breakout connector.  A sample design for a breakout panel is included in the CAM_Outputs folder.  GRBL always knows the direction of travel so individual min and max pins are not required.  Auto-squaring is supported by enabling ganged axes in GRBLHAL and setting the appropriate pins.

In addition to the limit signals, there are two probe input pins on the limit RJ45 breakout connector and the main PCB that are multiplexed via XOR logic and share a single input pin on the microcontroller..

For the dual-input signals there is no need to terminate unused ports.

The RJ45 pinout:

<img src="/readme_images/limit_rj45_pinout.jpg" width="150">

### User Buttons
<img src="/readme_images/User_mod_render.jpg" width="400">

Standard CNC functions are mapped to 4 inputs.  These signals are primarily intended to be used via the user RJ45 connector.  They are also exposed via 3 wire connections on the main PCB.  When multiplexed these signals must be NO logic.  A sample design for a button panel utilizing clear PETG buttons is included in the CAM_Outputs folder.

The RJ45 pinout:

<img src="/readme_images/user_rj45_pinout.jpg" width="150">

The HALT signal is not a safety feature and should not be used in place of a true electrical emergerncy stop.  It is intended to notify the controller of urgent requests and should be NO as it is shared between the PCB terminal block, RJ45 output and motor alarm.

### Spindle, Flood and Mist relay drivers - Auxillary relay drivers
The relay voltage is selectable between either the 12-24V input voltage, or the onboard 5V supply. P9 allows you to select the relay voltage.  If you need to drive more than 250 mA through the auxillary and mist/coolant relay outputs, larger external relays are likley required.

### Auxillary relay drivers
P9 allows you to select the relay voltage.  If you need to drive more than 250 mA through the auxillary and mist/coolant relay outputs, larger external relays are likley required.

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

This is a standard Raspberry PI GPIO header.  The Pi has the ability to drive the axuilliary outputs, as well as read the status of the real-time control signals.  Three Pi GPIO signals are also brought out to a header on the top side of the board.  The Flexi-HAL is capable of powering a Pi Zero W or PI 3 A+ directly, but it cannot supply enough current for a full Pi 3 or Pi4 - when using those you must supply power to the PI externally.  Please note that A6 revision PCBA require a 90 degree headerwith the Pi mounted in a vertical orientation.

For communicating with the Teensy, the Pi can act as a standard g-code sender and send data over the connected UART.  The I2C keypad interface is also connected to allow for the potential to develop pi based real-time controls.  Finally, the Pi can assert the user switch controls such as emergency stop.  GRBL auxillary inputs are also routed to the Pi in the event they are unused by the GRBLHAL application.  The Pi can also be used to drive the auxilliar relay outputs.

### Flexi-HAL EST Ecosystem
Complete Flexi-HAL ecosystem with Jogger, breakouts and motion controller.

<img src="/readme_images/EST_Ecosystem.png" width="500">

### Attributions
This project uses components from the very helpful actiBMS library for JLCPCB SMT parts.

https://github.com/actiBMS/JLCSMT_LIB



