# T41-2 Main Board Version 1.2

This is the PCB for the T41-2 Main Board module for the T41 "Software Defined Transceiver".
The PCB was designed using the open-source design tool Kicad 8.  The "teardrop" feature of
the layout tool was used to increase PCB reliability.

The T41-2 Main Board evolved from the original V011 design.

## Summary of Main Board Design Features

The display buffer device is retained.  The display buffer/driver device is required due to the
required high-speed operation of the SPI bus between the Teensy and the display.  The reason
to use the driver device is that the Teensy's "general purpose outputs" (GPOs) are weak.
With a specified maximum output of 4mA, reliability of the SPI interconnect to the display
cannot be guaranteed.  Note that the Teensy is re-oriented on the board such that the wiring
between the Teensy and driver device, and also between the output of the driver device and
the display connector, is minimized.  The display connector is moved to the bottom side of
the board in order to minimize the length of the cable between the Main Board and the display.

A jumper is provided to select the display voltage of either 3.3 or 5.0 volts.

An I2C bus is routed to both power distribution connector J1 and also optionally via jumper
resistors the filter connector J3.  Due to the loading inherent in the ribbon cables, an
I2C "re-driver" device (U4) is added to the design.  The re-driver is designed to handle a large
amount of capacitive load.  So far, testing indicates robust operation of the I2C bus
with this device in place.  It is planned to add I2C to other T41-2 modules in order to
simplify interconnect.  The re-driver device creates the foundation for the I2C expansion.

The Si5351 PLL device is removed.  It is recommended to use an external Si5351 module instead.
For more information, please refer to this repository:

<https://github.com/Greg-R/bracket_dual_T41>

Note that the QSE2DC/QSE3DCEZ boards include a connector to provide power and I2C to the
Si5351 module.  It should be easy to "hack" these connections to an older QSD or QSE board.

The same data converter modules, the PCM5102 and PCM1808, are retained.  Pin spacing issues
of the old Main Board have been resolved.

The audio amplifier is removed.  A 4-pin connector is provided which includes audio out,
amplifier enable, and ground.  Design files for a 3D printed bracket for an off-the-shelf audio
amplifier module are available here:

<https://github.com/Greg-R/SDT_Case_KF5N/tree/main/bracket>

The amplifier used in the KF5N T41 is available here:

<https://www.amazon.com/gp/product/B07F596XMX>

Of course, other audio amplifier modules can be used.  Be sure to choose a module which
includes an enable/disable control.

Interconnect to the radio modules and external hardware is fully covered.
Here is a list of the interconnect:

1.  J1.  Primary power distribution.  Reset for QSD2 and QSE2 boards.  TXRX signal.
         I2C bus replaces the speaker output of the original Main Board.
2.  J2:  Volume and Fine Tune encoder connections.
3.  J3:  Low-Pass Filter controls.  This now includes I2C and power for future designs.
4.  J4:  Tune and Filter encoder connections.
5.  J5:  Display connector.
6.  J6:  3.5mm stereo connector to QSD.
7.  J7:  3.5mm stereo connector to QSE.  If sideband is flipped, flip the connector
         at the Audio Adapter.
8.  J8:  3.5mm stereo connector to microphone.  Tip is audio.  Ring is PTT.
9.  J10: 3.5mm stereo connector to key.  Tip is straight key and dit.  Ring is dah.
10. J13: Switch matrix.  3.3 volt reference from Teensy, input to Teensy ADC and ground.
11. J15: Analog audio output and audio amplifier enable control.
12. J16: Display power jumper, 3.3 or 5.0 volts.

Power supply decoupling (ferrite beads and bypass capacitors) have been added to critical
interfaces.  Perhaps one of the most critical is the Low Pass Filter interface.  This
interface is exposed to the full RF output power of the transmitter.  This interface has
been thoroughly decoupled with ferrite beads and shunt capacitors.

Gerber files for PCB fabrication are included in the gerbers folder.
A PDF of the schematic  is included for quick viewing of the circuit design in the doc
folder.

### T41EEE Arduino Sketch with Special Features for T41-2 Main Board

The Arduino sketch designed to work with the T41-2 Main Board is located here:

<https://github.com/Greg-R/T41EEE>

You will need to modify the file "MyConfigurationFile.h".  The default code looks like this:

    // Set multiplication factors for your QSD and QSE boards.
    #define MASTER_CLK_MULT_RX 4
    #define MASTER_CLK_MULT_TX 4

    // Uncomment this line for QSE2.
    //#define QSE2

Assuming you are using both QSD2 and QSE2DCEZ, you should change the code to this:

    // Set multiplication factors for your QSD and QSE boards.
    #define MASTER_CLK_MULT_RX 2
    #define MASTER_CLK_MULT_TX 2

    // Uncomment this line for QSE2.
    #define QSE2

To use the Si5351 module, there is another line to uncomment:

    // Uncomment this line if using an external PLL module.
    #define PLLMODULE

There are more changes required to use the external audio amplifier.
The example below shows how the code should appear when using the T41-2 Main Board
and the audio amplifier referenced above:

    // Uncomment for the original T41 audio mute control.
    //#define UNMUTEAUDIO LOW
    //#define MUTEAUDIO   HIGH
    // Use this for external amp with mute LOW, unmute HIGH.
    #define UNMUTEAUDIO HIGH
    #define MUTEAUDIO   LOW

// If using an external amplifier, the gain may need to be adjusted for the best volume range.
#define AUDIOSCALE  40    // A typical value is 20.  Increase or decrease this value depending on your amplifier gain.

### Power Supply Decoupling and Regulators

The power supplies routed through the 16 wire ribbon cable are prone to noise, most likely from the Main board.
Regulators for the three required voltages include low-esr bypass capacitors.  This will provide clean bias sources
to the modulator circuit.

### Connectors

The I/Q output connector is changed to surface mount.  The RF SMA connectors are the same, or they can be changed to right-angle
connectors to improve coaxial cable routing.  The 16 pin IDC connector is the same as the V011 board.

### PCB Layout

The PCB layout was completed using the open-source tool Kicad version 8.

Gerber files are included in the gerber folder.  The prototype PCBs were fabricated by PCBWay at a cost of US$1.00 each.
Shipping was about US$25.00 for quantity 5 boards.  JLCPCB can be used, and has the option of cheaper, slower shipping.

## Bill Of Material (BOM)

A public Digikey BOM is here:

<https://www.digikey.com/en/mylists/list/XQQB1BJVVE>

Please note that specific parts may or may not be available when attempting to order.  It is the responsibility of the builder
to find subsitutes as required.

## Build Tips

In general, the T41-2 Main Board is easier to build than the original V010/V011 series boards.  There are a few items to be aware of to avoid
build errors.

The most probable error(s) are incorrect orientation of the flip-flop, multiplexer, transformer, and differential amplifier devices.
The pin 1 markings on the devices are very difficult to see.  The transformer markings are easy to read; this should be clear in the
photograph of the finished board.

Here are details on each part with regards to proper orientation of the board.  The descriptions are viewing the top side of the board,
with the "T41 EXPERIMENTERS PLATFORM" near the bottom of the board in normal left-to-right reading orientation.

### Transformer TR2

The transformer TR2 should be placed with the "dot" at the upper left corner.  If you are using the ADT1-1+, it appears
to be symmetrical, so the orientation shouldn't matter.  However, other transformers may require a specific orientation.

### 74AC74 Dual Flip-Flop U4

Pin 1 should be in the lower left corner.  This is marked with a small white dot on the PCB.

### 3253 Multiplexer U3

Pin 1 is at the upper right.  Look for a small white dot next to pin 1.

### Differential Amplifiers AD8137 U5 and U9

Pin 1 indicated with a dot on the PCB.  Both parts are oriented the same, with Pin 1 towards the upper right.
There is a dot on the parts to indicate Pin 1, however, it is very hard to see.  The package is beveled on the Pin 1 side; this is easy to see.

### Non-placed Parts

Don't place R3.  Placing this jumper implements shutdown of the differential amplifiers during receive.  This feature has not been
tested.

Don't place J7, R5, and R7.  These parts are for future use with an Si5351 module.

The two-pin header J6 can be optionally placed if it is convenient to route the TXRX signal from the QSE2DCEZ board.

### Bottom Side Parts

There are 5 capacitors and 1 resistor on the bottom side of the PCB.  These should be soldered last.  I was able to use a hot air gun without
any problems with the top-side components desoldering.

### High Resolution Photos of T41-2 Main Board

Links to photos of a fully constructed T41-2 Main Board follow.

<https://drive.google.com/file/d/1NHmCX63jgQk1Dq1cA9cc_mSMycnhU9X6/view?usp=sharing>

<https://drive.google.com/file/d/1X9l9AiZiDQOiTOdKtAZ-9BbhG-T6EKmi/view?usp=sharing>

<https://drive.google.com/file/d/1b3odUEUUSwXo5qLtaAhQx5JJ_SECJyb-/view?usp=sharing>

## References and Further Reading

1.  "Digital Signal Processing and Software Defined Radio, Theory and Construction of the T41-EP Software Defined Transceiver",
by Albert F. Peter, AC8GY, and Dr. Jack Purdum, W8TEE.  This is the ultimate resource for builders of the T41 Software Defined
Transceiver:  <https://www.amazon.com/Digital-Signal-Processing-Software-Defined/dp/B0D25FV48C>
2.  QSD2:
    <https://github.com/Greg-R/qsd2>
3.  QSE2:
    <https://github.com/Greg-R/qse2>
4.  QSE-Filter:
    <https://github.com/Greg-R/QSE-Filter>
6.  T41-2 Power Module:
    <https://github.com/Greg-R/T41-2_Power>
5.  Dual bracket and Si5351 external module:
    <https://github.com/Greg-R/bracket_dual_T41>
6.  T41EEE software for the T41 transceiver:
    <https://github.com/Greg-R/T41EEE>


