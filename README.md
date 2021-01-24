# i2c2midi

i2c2midi is a simple DIY 3 hp eurorack module that speaks I2C and MIDI. It's primarily build to be used together with [monome Teletype](https://monome.org/docs/teletype/). It receives I2C messages and converts them to either MIDI notes or MIDI CC messages.

## Details
- DIY eurorack module 
- 3 hp
- Inputs: I2C 3 pin
- Outputs: MIDI TRS (Type A)
- Teensy 3.2

## Pictures 

![](pictures/i2c2midi_top.jpg)

![](pictures/i2c2midi_side.jpg)

## Repository
- `firmware` — The Teensy/Arduino code that converts I2C messages to MIDI out messages.
- `hardware` — Everything hardware related
  - Schematic
  - Protoboard layout (with and without components)
  - Fritzing file
  - Important note about power cabling (!)
- `pictures` — Some pictures of the first working prototype.
  
## About the firmware

As of now, the firmware is written specifically for I2C messages sent from [monome Teletype](https://monome.org/docs/teletype/) using the [disting Ex MIDI OPs](https://github.com/scanner-darkly/teletype/wiki/DISTING-EX-INTEGRATION) by [scanner-darkly](https://github.com/scanner-darkly). The i2c2midi module “poses” as a second disting.
Based on that setup, there are few things to note and are hardcoded within the firmware:

- The following addresses are specifically reserved for the disting EX within the Teletype firmware: `0x41`, `0x42`, `0x43`, `0x44` (65, 66, 67, 68). Since the module should act like a second disting EX, it is set up to listen to I2C messages on address `0x42` (66). This could be changed to act as the first (65), third (67) or fourth (68) disting EX instead.

- The I2C messages for the disting Ex are constructed as `<address> <command> <optional bytes according to command>`. See [here](https://github.com/scanner-darkly/teletype/wiki/DISTING-EX-I2C-SPECIFICATION) or [here](https://www.expert-sleepers.co.uk/distingEXfirmwareupdates.html) for further details. 

- The “send MIDI message” command of the disting Ex is `0x4F`(79) and is constructed as `<address> 0x4F <status> <optional data byte 0> <optional data byte 1>`.

- `<status>`refers to the [MIDI status](https://www.midimountain.com/midi/midi_status.htm). MIDI note on messages have the status 144-159 for channels 1-16. MIDI CC messages have the status 176-191 for channels 1-16.

- Sending a MIDI note on (note 48, velocity 127, channel 1) therefore looks like this: `0x42 0x4F 144 48 127`.

- Sending a MIDI CC (controller 1, value 60, channel 1) therefore looks like this: `0x42 0x4F 176 1 60`.

## Protoboard layout

![](hardware/i2c2midi_protoboard.png)

## Schematic

![](hardware/i2c2midi_schematic.png)

## Powering it from a Eurorack bus board

As of now, I went with a dirty hack to power the module directly from a Eurorack case. Please be warned – *this is NOT a good solution and one should proceed with absolute caution!*
In order to power the Teensy via its VIN pin (5V-6V tolerant), I use a standard eurorack 16 to 10 pin power cable – with the 16 pin jack plugged to the bus board ***UPSIDE DOWN***. See the illustration ([B]) below:

![](hardware/i2c2midi_reversed_cable.png)


