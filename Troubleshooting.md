# Troubleshooting Guide

Some guidance on common problems. The description of the expected initialisation sequence may also help to diagnose the issue.

##  Breadboard Wiring
If you have wired the adapter yourself on a breadboard and it is not communicating with the joystick, or the force feedback is not working, a loose connection or wiring error is often the cause.

Check the layout first. If you are using an 
ATmega32U4 5V 16 MHz board other than the Teensy you will need to read across the AVR pinouts 
e.g. [Leonardo](https://docs.arduino.cc/resources/pinouts/A000057-full-pinout.pdf), [Pro Micro](https://golem.hu/article/pro-micro-pinout/), [Arduino Micro](https://docs.arduino.cc/resources/pinouts/A000053-full-pinout.pdf).

You will also need to swap PB0 for PB6 if you are using a *_Micro.hex

Check all the connections are solid and test continuity from the gameport socket to the board with a multimeter.

## Capacitors
The initialisation of Force Feedback mode in the joystick requires tight timing of pulses and seems to be sensitive to the value of capacitance. If the 1nf capacitors in the build guide don't work for you, some people have reported success with either no capacitors or higher values e.g. 1.5nf, 2.2nf.
If you are going from a breadboard to a soldered adapter on perf/stripboard or a PCB, be aware this may also reduce the capacitance due to the shorter wires. 

## USB port
USB micro connectors on dev boards can be temperamental. Try fully removing and reinserting it and observe the LED sequence to confirm it doesn't enter the bootloader first, as this seems to prevent the adapter being recognised later as a USB device.  
Also try changing to a different USB port on the PC. Sometimes Windows will refuse to reconnect to the adapter unless you do this. Rebooting the PC may also help.

## Firmware
Check you are using the appropriate variant of the firmware for your boards pinout and LED ports. Although the microcontroller is the same, bootloaders differ between board manufacturers so make sure to use the appropriate procedure for flashing.
Try flashing the firmware again - very occasionally flashing may fail the first time.
After flashing the firmware fully remove and reinsert the USB connector otherwise the adapter may not be recognised by the host PC.

## Joystick Function
The FFP joystick hardware is getting on so worth testing. Even prior to building an adapter you can confirm the basic functions. If you connect 5V across the gameport power and ground pins and then plug in the 12V power supply you should get a solid green LED on the joystick base and a self centering spring effect when you grip the handle (There is a light gate in the handle that only activates the motors when your hand is present). 
Any of the commercial gameport to USB adapters will supply power, including the one that came with the Precision Pro. 

[Note: The behaviour is different for the Sidewinder wheel]

## Initialisation Sequence
Following through the expected sequence and observing where it fails can help to find the problem.
These notes are for the joystick - the wheel is different. Steps on PC are described for Windows 10, but there will be an equivalent for other operating systems. 
**Note the adapter will not appear as a USB device without the joystick.**

Starting with everything unplugged
1. Connect the gameport plug from the joystick to the adapter
2. Connect a USB cable from the PC to the adapter. The following should then be observed (may only take a couple of seconds)
* The green LED at the base of the joystick begins flashing
* The Board Tx/Rx LEDs (not the power LED) should begin to flash, switching between on and off 3 times per second. If it is either solid on or off then you may have the wrong firmware or pinout wiring for your board. If it pulses once per second or other pattern then you may have entered the bootloader instead - you can compare the bootloader LED sequence by comparing what happens when you reset your board (there may be a button or you may need to short the RST pin to GND)
* After a few moments the Board Tx/Rx LEDs will change to solid on. This indicates firstly a connection to the joystick has been established and secondly the adapter has begun communicating over USB with the host. Alternatively if the adapter fails to connect to the joystick then the adapter will not try to establish a USB connection and will therefore not be recognised as a USB device.
* After a few moments the Board Tx/Rx LEDs will turn off. This means a USB connection has been established with the host PC and enumeration was successful. At the same time you should hear the ping from your PC and the adapter will appear in device manager (Windows).
3. Now connect the power cable to the joystick. The green LED at the base of the joystick will be solid on - this will be solid on so long as there is power from both gameport and the 12V brick power supply, irrespective of communications. With the newer versions of the firmware (v0.5beta) you will feel the default spring centering effect when gripping the handle. If you get spring centering with the older _r54 hex that is a sign that force feedback mode has not been activated.
4. Open the "Set up USB Game Controllers" dialog from Control Panel and confirm the buttons and axes are behaving as expected.
5. Open Fedit.exe. The spring centering will be disabled and the joystick should go limp. You should observe the board Tx/Rx LEDs flash whenever a force feedback command is sent to the joystick. If you get "Warning: Could not find a Force Feedback device" then the adapter is not enumerating itself as a valid force feedback device (or the USB connection has dropped). If there are no errors but effects can't be felt when played in fedit.exe or games then FFB mode may not have been successfully activated - check the wiring and try different capacitors.

## It's still not working!
If you are seeking help, you are most likely to get useful advice if you include:
* Where the sequence above fails for you;
* Anything else you have tried;
* The board you are using;
* The name and origin of the firmware file;
* Your operating system;
