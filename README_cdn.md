![Logo](/readme_images/logo_sm.jpg)
# Flexi-HAL CNC Controller
<img src="/readme_images/Board_Photo.jpg" width="800">


Expatria Technologies GRBLHAL and LinuxCNC (and more!) CNC control board

Currently available in our online store:

https://expatria.myshopify.com/products/flexi-hal

Please consider buying a board to support our open-source designs. 

The Flexi-HAL was designed to be an EMI resistant IO platform for any microcontroller based CNC/motion control firmware or software.  This board includes a few features that we couldn't find on other boards, and it reduces the amount of extra wiring in our setups.  In the co-operative spirit of the PrintNC and other CNC communities, and Open Source Hardware, the Flexi-HAL will be licensed and free to use by all parties, including commercial parties, under the CERN-OHL-S V2 license.  It is our hope that the community finds the design useful and that it may be carried forward to help advance the PrintNC and broader CNC hobby community.

The Flexi-HAL incorporates community driven elements from the PrintNC Electronic Standardization (EST) Project.  As part of this project, two additional breakout boards have been created for the user controls and limits/probe inputs.  These are simple boards and could easily be milled and hand assembled, but fabrication files for each are available in the CAM_Outputs folder.  The inputs are accessible via the RJ45 connectors on the Flexi-HAL mainboard.  In addition, the Flexi-HAL is intended to be used with the Expatria Real-Time jog controller or similar peripheral:

https://github.com/Expatria-Technologies/RT_Jog_Controller

The key features of the Flexi-HAL:

1) 5 Axis of isolated step/dir motor control featuring high-speed digital isolators and differential signal drivers for maximum signal integrity and step rates.
2) Integrated support for 3 wire inductive type powered switches.
3) Onboard 5V power regulator.
4) Integrated RS485 with automatic direction control.
5) Support for closed loop stepper motors and servos with alarm feedback.
6) Differential interface for a spindle encoder inputC.
7) Real-time control port for remote handwheels and pendants.
8) Raspberry Pi GPIO connector allows integration of sender software and allows the board to host a full LinuxCNC installation.
9) 10V or 5V spindle control including PWM output via selectable jumpers.
10) 3 wire (powered) connections for standard CNC buttons on a breakout RJ45 connector that utilizes components compatible with the PrintNC EST project.
11) XYZA limit switches and probe/toolsetter on breakout RJ45 connector.
12) Flood/Mist/Spindle relay drivers.
13) Additional auxilliary inputs and relay driver outputs.
14) All machine facing IO is galvanically isolated from the MCU and user interfaces.
15) Easy reliable USB-C connection to a PC
16) GRBLHAL Ethernet Websockets or Telnet communication options with uFlexiNET module.
17) GRBLHAL SD card G-Code streaming and macro/subroutine storage (including looping and conditional execution) with uFelxiNET module.

Optimized GRBLHAL driver is located here:  
https://github.com/Expatria-Technologies/STM32F4xx/releases

Source code for the customized Remora component to run in LinuxCNC is located here:  
https://github.com/Expatria-Technologies/remora-flexi-hal

Prebuilt Raspberry Pi 4B image is located here:  
https://github.com/Expatria-Technologies/remora-flexi-hal/releases

Accessory Breakout PCBs are located here:  
https://github.com/Expatria-Technologies/EST_Accessory_PCB

Various community mods and accessories are located here:  
https://github.com/Expatria-Technologies/Mods-Accessories

Recommended GRBLHAL Post Processor is here:  
https://github.com/Dietz0r/grblHAL_Fusion360_Post_Processor

uFlexiNET expansion card is here:  
https://github.com/Expatria-Technologies/uFlexiNET

The uf2 bootloader can be found here:  
https://github.com/Expatria-Technologies/tinyuf2/releases

## Flexi-HAL Overview

<img src="/readme_images/Board_Overview.png" width="700">

Bottom side pin labels:

<img src="/readme_images/backside.png" width="700">

Pinout List:

<img src="/readme_images/Pinout.png" width="900">

### STM32F446 Microcontroller

Primarily, the board supports both GRBLHAL and LinuxCNC.  Binary firmware builds for different axis configurations are published on the Expatria Github.  In addition, a customized port of the awesome Remora project has been developed alongside Flexi-HAL so that you can easily switch to LinuxCNC.  With LinuxCNC, a Raspberry Pi 4b is installed in the Pi GPIO header to ensure the best possible signal integrity for the SPI step generation interface.

By using a common STM32F4 MCU, the Flexi-HAL is also able to easily host ports of uCNC, Marlin and even Klipper so it can drive much more than just CNC machines.

### UF2 Bootloader
The Flexi-HAL is meant to be used with a UF2 bootloader. All boards from Expatria will come with this bootloader installed. This allows you to upgrade or change the firmware on the flexi as easily as copying a file to a USB drive.  Pre-built binary firmwares from Expatria are distributed as UF2 files.  There are two ways to access bootloader mode and change the firmware:
1) Power up the MCU section (connect the USB cable) while holding the CYC/ST and FD/HLD buttons.
2) Double-tap the reset (RST) button on the side of the board near the USBC port.

Once in bootloader mode, the Flexi will appear as a USB storage device called "FLEXI446" and the X limit light will flash briefly if there is no signal asserted on that pin (switch is open).  Simply copy the new firwmare to this USB drive and the board will automatically install it and reboot.  Some operating systems may give an error when flashing is complete because the 'disk' was removed without ejecting it.  This error can be ignored.

### Power Input
<img src="/readme_images/isolation_zones.png" width="500">

The Flexi-HAL features the capability for full power and ground isolation between the sensitive microncontroller and host circuits, and the external IO that extends out to the rest of the machine.  There is a single input for 12-24VDC.  The board has its own onboard 5V regulator to power the stepper drivers and external RS485 interface.  There is also a small capacity 12V LDO that is specifically for driving the limit and user switches, as well as optionally providing the base voltage for the 10V spindle output.

Flexi-HAL has reverse polarity as well as over-current protection beyond 1A.  This is important to consider when using external relays that draw a lot of current as this may overwhelm the capacity of the board.  If you need to drive more than 250 mA through the auxillary and mist/coolant relay outputs, external relays are likely required.

<img src="/readme_images/power_bypass.jpg" width="500">
Normally the MCU and RPI header will be powered via the USBC connector.  The Jog2K is also powered from the isolated domain.  By installing two jumpers on the above offset pins, the 5V power and ground isolation can be bypassed and the Flexi-HAL will operate without an external 5V supply in a semi-isolated state.  This does reduce the EMI resistance of the board and is not recommended when sending Gcode via the USBC connector.

Note: With the isolation jumpers not-populatd, and the Flexi-HAL connected to 12-24v power, it may appear that the board is ready to run as some LEDs illuminate. You MUST provide 5v power to the isolated domain (MCU, Jogger) either through USB or the bypass jumpers in order for the MCU and Jogger to turn on!

### Stepper Drivers
<img src="/readme_images/Stepper_Pins.jpg" width="300">

The stepper drivers are designed to be used with IDC connectors that are quick to assemble.  Unfortunately you will need to ensure that at the external driver the high and low signal pairs are connected correctly as there is no standard pinout on these drivers.  The 8 pin connection allows you to run a high and low pair for every signal to ensure the best possible signal integrity.  The Flexi-HAL uses high speed digital isolators and differential RS-422 style signal drivers for the motion signals.

When using the alarm input the external drivers need to be configured for open-drain, active-low output.  The alarm output must be high impedance during normal operation.  In normal operation the blue MOTOR LED will be lit indicating that there are no active alarms.

Typical wiring for most open-loop stepper drivers: 

![image](https://github.com/Expatria-Technologies/Flexi-HAL/assets/6061539/89e5df1e-06ef-4319-acb0-a30ee8e6447b)


### Analog Spindle Control

Traditional GRBL spindle control interface for 0-10V or 0-5V spindle control.  Uses a dual-stage output driver for linear response.  Spindle power supply is selectable between 5V, 12V or external.  Note that when running the board with less than 14v input, it may not be possible to reach the full 10V output level - this is due to the dropout of the 12V LDO.  In this case, use the external spindle power input to connect your 12v and bypass the LDO for the spindle control voltage.

<img src="/readme_images/Spindle_PWM_Config.jpg" width="500">

Near the main power input of the Flexi-HAL there is a diagram showing how a set of jumpers may be configured to enable 0-10V analog or TTL PWM output.  This jumper allows you to have a 12V compliant TTL PWM signal to drive a device like a laser engraver or an ESC.

Two headers - 3 pin P6 and 2 pin P16, connect the analog spindle section to the rest of the board.  P6 allows you to power the spindle section from either the onboard 5V or 12V rails.  If you remove P6 and P16 then the spindle section is completely isolated from the rest of the system and in this configuration can be used to drive motor controllers that require a floating control voltage.  Also, when driving the board with less than 14V input, it may not be possible to adjust the spindle output voltage to the full 10V.  In this case we recommend removing P6 (leave P16 in place) and applying 12V to the external spindle supply input directly.


### RS485 Spindle Control
This interface is primarily intended to be used with a Huanyang style VFD for spindle control.  The A, B and G (common) pins are marked on the top and bottoms sides of the PCB.  Simply connect the appropriate pins to the terminals on the VFD.  Note that the G pin should be used for signal common, it should not be connected to the shield of a shielded cable.

https://github.com/grblHAL/Plugins_spindle/

### 5 Axis limit inputs

By default both GRBL and the Flexi-HAL expect NPN NC limit switches.  PNP switches are not supported. NO switches can also be used on any switch input.

The first four axes have single limit inputs that are also accessible via the RJ45 EST limit breakout connector.  GRBL always knows the direction of travel so individual min and max pins are not required.  Auto-squaring is supported by enabling ganged axes in GRBLHAL and setting the appropriate pins.

In addition to the limit signals, there are two probe input pins on the limit RJ45 breakout connector and the main PCB that are multiplexed via XOR logic and share a single input pin on the microcontroller.

<img src="/readme_images/XOR_switches.gif" width="300">

For the dual-input signals there is no need to terminate unused ports.

The RJ45 pinout:

<img src="/readme_images/limit_rj45_pinout.jpg" width="150">

### User Buttons

Standard CNC functions are mapped to 4 inputs.  These signals are primarily intended to be used via the user RJ45 connector.  They are also exposed via 3 wire connections on the main PCB.  These inputs have the same circuitry as the limit inputs and are NPN.  Connect SIG and GND to assert the signal.  When multiplexed these signals must be NO logic.

The RJ45 pinout:

<img src="/readme_images/user_rj45_pinout.jpg" width="150">

The HALT signal is not a safety feature and should not be used in place of a true electrical emergerncy stop.  It is intended to notify the controller of urgent requests and should be NO as it is shared between the PCB terminal block, RJ45 output and motor alarm.

### HALT Polarity Selection

<img src="/readme_images/haltsel.png" width="400">

Starting from A5 revision, the HALT signals from the Flexi-HAL board header and the RJ45 user button breakout are connected via an XOR gate. The polarity of the signal to the MCU can be inverted by moving the jumper P12 pictured above. The default state (as shown / rightmost two pins) is suitable for use with NO switches or if NC switches are connected to both the Flexi-HAL header and the RJ45 breakout. 

Moving the jumper to the leftmost two pins allows you to use a single NC switch connected to the Flexi-HAL header or RJ45 breakout, with or without a NO switch connected to the other. The typical use case for this alternate configuration is with an NC overtravel sensor or NC e-stop circuit without the need for an external relay. 

If you are in doubt of the correct header position for your case, you may simply try both positions and choose the one where the HALT signal is not asserted (red light is not on) when in the nominal operating condition.

### Spindle, Flood and Mist relay drivers
The Spindle, Flood and Mist relay outputs are driven from the main board supply.  External relays should be selected to match the power supplied to the Flexi-HAL.  The absolute maximum coil current for each output should not exceed 250mA.  In general, the coil resistance should be 150 Ohm or greater.  The relay outputs are active-low, the high side is connected to the main power input.

### Auxillary relay drivers
<img src="/readme_images/AUX_POWER.jpg" width="200">
Four axilliary relay outputs are exposed.  These have a maximum combined drive current of 1000 mA when operated via a dedicated power supply.  The relay voltage can be selected via a 3 pin jumper between the main board power supply, the onboard 12V supply and the onboard 5V supply.  If the jumper is left unpopulated power for the aux outputs is supplied via the dedicated (fused and polarity protected) input.  The 12V and 5V options cannot drive more than 20 mA per pin and are only used for TTL signalling applications - they should never be used to drive inductive loads.  Never populate P17 and the external supply at the same time.  When driving external relays, the coil resistance should be 150 Ohm or greater.  The relay outputs are active-low, the high side is connected via the power selection jumper.

### Real-Time Control Port
<img src="/readme_images/Jog2k_Enclosure_2.png" width="500">
This port is intended to allow for external pendant type devices to issue real-time jogging and override controls to the motion  controller.  It uses I2C signalling and adds additional signals for the keypad interrupt as well as the Halt signal.  We feel that a robust and wired control is the safest way to interact with a CNC machine in real time.  A simple reference controller implementation is under active development, but there are some code examples referenced in the GRBLHAL I2C keypad plugin repository:

https://github.com/grblHAL/Plugin_I2C_keypad/

https://github.com/Expatria-Technologies/RT_Jog_Controller/

### Spindle Sync Port
This port allows a differential connection to an external module for a robust GRBLHAL lathe implementation or to support a high-speed encoder input for LinuxCNC.  An encoder such as E6B2-CWZ1X is suitable for most spindle applications.

There is also a small breakout available that exposes these signals for 5V single-ended NPN input to interface with more basic sensors and other switched inputs.

The RJ45 pinout:

<img src="/readme_images/encoder_rj45_pinout.jpg" width="150">

### Raspberry PI expansion header
<img src="/readme_images/Pi_Installed.png" width="500">
The Rasberry Pi GPIO header allows the Flexi-HAL to host a full Raspberry Pi type SBC.  This allows the platform to support LinuxCNC via the Remora project.

<img src="/readme_images/Pi_Pinout.jpg" width="500">

### Accessories
Some 3D printed accessories are avilable in [Mods & Accessories](https://github.com/Expatria-Technologies/Mods-Accessories/), including a DIN rail mount and enclosures/mounts for the limit and button breakouts.

### Default Jumper Locations
![image](https://github.com/Expatria-Technologies/Flexi-HAL/assets/6061539/06c76aa8-7ccc-4621-a8f2-30bbd142a144)
By default the following jumpers (shown in the graphic in RED) should be populated.
This has the following effects:
The RPI Header uart is connected to the internal MCU UART - this is necessary to flash Remora firmware from the RPI and also is used with the uFlexiNET module for the SD card chip select.
The HALT polarity is set for use with an NO button like the button breakout.
The analog spindle output is set for 0-10V.
The analog spindle section is connected to the onboard 12V supply.
The analog spindle section ground is connected to the PCB external ground.
The aux outputs are powered via the main 24V input.


### Example Wiring Diagram
A comprehensive wiring diagram has been developed by the PrintNC community using the FlexiHAL.  While not specific to Flexi, this gives a great example of how to connect the board into the rest of a complete electronics box to drive a CNC machine. 
https://wiki.printnc.info/en/v3/wiring

### Attributions
This project uses components from the very helpful actiBMS library.

https://github.com/actiBMS/JLCSMT_LIB



