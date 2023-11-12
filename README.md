# agon-vdp

**NOT** Part of the official Quark firmware for the Agon series of microcomputers.
This fork is to test ideas, and make a firmware for me. It will be updated with
most, if not all upstream official things, but might contain experiments and
fix tests of any found bugs.

### Documentation

The AGON documentation can now be found on the [Agon Light Documentation Wiki](https://github.com/breakintoprogram/agon-docs/wiki)

### Building

The ESP32 is programmed via the USB connector at the top of the board using the Arduino IDE. It has been tested on version 1.8.19 and the latest 2.0.4.

#### Arduino IDE settings

In order to add the ESP32 as a supported board in the Arduino IDE, you will need to add in this URL into the board manager:

Select Preferences from the File menu

In the Additional Board Manager URLs text box, enter the following URL:

`https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`

In the Board Manager (from the Tools menu), select the esp32 board. Advice for the original Quark firmware was to make sure version 2.0.4 is installed, however for the Agon Console8 development has been conducted with version 2.0.11.

Now the board can be selected and configured:

* Board: “ESP32 Dev Module”
* Upload Speed: "115200"
* CPU Frequency: “240Mhz (WiFi/BT)”
* Flash Frequency: “80Mhz”
* Flash Mode: “QIO”
* Flash Size: “4MB (32Mb)”
* Partition Scheme: “Default 4MB with spiffs (1.2MB APP/1.5MB SPIFFS)”
* Core Debug Level: “None”
* PSRAM: “Enabled”

Bernado writes:

> Although I am using 4MB for the PSRAM coupled to the ESP32 (the FabGL assumption), that memory actually has 8MB, so if you find a way to use the extra 4MB, please change the configuration to 8MB.

(Accessing the upper 4MB of PSRAM requires a different method of memory access, making use of the [Himem Allocation API](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/himem.html).  This is not currently used in the underlying fag-gl/vdp-gl library and not used in any other VDP code, but may be in the future.)

And for the Port, you will need to determine the Com port that the Agon Light is assigned from your OS after it is connected.

Now the third party libraries will need to be installed from the Library Manager in the Tools menu

* vdp-gl version 1.0.3 (Official fork of FabGL 1.0.8 for Agon)
* ESP32Time version 2.0.4

Original Quark firmware advice was to use ESP32Time version 2.0.0, however for the Agon Console8 development has been conducted with version 2.0.4.  There are no guarantees that the code will work correctly with other versions, however if the major version number matches it is likely that it will.

NB:

- If you have previously installed FabGL as a third-party library in the Arduino IDE, please remove it before installing vdp-gl.
- If you are using version 2.0.x of the IDE and get the following message during the upload stage: `ModuleNotFoundError: No module named 'serial'` then you will need to install the python3-serial package.
- It may be possible to communicate with your Agon at higher speeds than 115200, but this is the speed that has been tested and is known to work for Agon Console8 development on a MacBook Pro with an M1Max CPU.  There is no harm in trying higher speeds.
