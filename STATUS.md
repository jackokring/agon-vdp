# Modding Status

The development kit works on Arduino and so `linux-arm64` is fine for development.
My understanding is that if the MOS is not altered, and the "VDP data protocol" remains
the same then there should be no problems flashing this VDP.

## Arduino IDE Loads and All is Well (v6.66 is as good a number as any)

The code is well structured, which is good.

 * esp32 2.0.11 board files
 * vdp-gl 1.0.3
 * ESP32Time 2.0.4

are used. This may change later (check `platformio.ini` for dependancies).

 * `VDU` basically a character echo with control sequences running on the VDP. 
 Some `VDU` initial control codes have no implemented meaning.
   * **0** nothing. So should remain fixed.
   * 1 send to printer only.
     * So what is a "printer" in context of WWW and WiFi? 
   * 2 print on.
   * 3 print off.
   * 6 enable screen. Opposite of 21.
   * **27** send next literal less than 32 to screen.
     * I've requisitioned this one.
   * 20 restore colours.
   * 21 disable screen.
   * **23** is heavily overloaded on the byte values below 32 as they
   have no UDG.
     * **127** is also not a real UDG, but is likely a font glyph (unprinted),
     given that bitmaps are rectangular. This is perhaps the ideal intercept.
     The `VDU 23, 127` delivers the © symbol. Ironic in that it is impossible
     to place in code without printing it by a numeric VDU.
     * **0, $FD** so `VDU 23, 0, $FD, n` enters or exits the `WANSI` terminal
     mode. This is a mode where keycodes are processed normally so no key
     driver is needed. There is an intercept on `^W` ASCII `ETB` to get
     `VDU 23, ...` working. Being terminal though no attempt is made to
     synchronize the VDP cursor with the terminal cursor. By default
     the terminal is `ANSI` with cursor and `DEL` translation to use BBC
     style navigation.
 * `Mode 7` codes 128+ (0, 14, 15, 16, 27) technically free control codes.
   * Apparently I've done 24 instead of 25 lines.
     * I think it might encourage some restriction thinking.
     * Hi Mr. Oric, and wasn't line 25 local "tooltip" on the Oracle's Ceefax?
     Down on the OC Prestel, ancient of terminals?
   * Chopping out the unreferenced parts of the font and noticing height is
   less than 500 always seems to save about 1% of the flash space.
   This might be useful later with trimming the 20th row and `<WiFi.h>`.
   * Alternate colour palette as it's a 16 colour mode really.
     * `138` (End Box) does alternate foreground colour.
     * `139` (Start Box) does alternate background colour from foreground.
 * `Mode 135` is 16 colour 256 by 384 modulo mode. Double height Speccy mode.
 * `Audio` yes, `VDU 23, 0, &85, chan, 13, wave, (sampId;) dest_chan, mul-1, div-1`
 performs FM modulation of `dest_chan` by `chan`. The destination frequency value
 becomes non-functional while a modulator is enabled. Change the volume of
 `chan` for depth of modulation. Change the waveform of `chan` to perhaps
 delete the modulator. Use zero biased `mul` and `div` to set the modulation
 ratio applied to the `dest` channel. The source `chan` always runs at the
 programmed frequency note. Applying more than 1 modulator to a channel has
 undefined behaviour.
 * `VDU 23, 0, &C1, n` altered so maybe one day MOS will send byte `$040043` as
 `n` when running binaries for version control. Oh, I can dream. It's my fork.
 I checked, MOS maybe thinks it's a string NUL terminal.
 * Notice `USERSPACE` is defined in the emulator VDP `Makefile`.
 * `-emulation-kill` file suffix for compile for emulator "red zone".

## The Emulator VDP

The emulator uses a customized `vdp-gl`, so altered files can be
placed atop a copy of the quark104. Or after some VDP `Makefile` edits.

`./fab-agon-emulator --vdp ./src/vdp/vdp_quark666.so`

The emulator throws a segmentation fault panic when closed. This happens late
in the shutdown phase, after most of the threads have finished. This is
likely related to closing and unloading the dynamic library.

The repository builds with `PlatformIO` using VSCode, and via the command line
using an install of `sudo install pipx` making the commands from
`pipx install platformio`. This makes `pio` available by adding it to `$PATH`.

[The Emulator Fork](https://github.com/jackokring/fab-agon-emulator/tree/main)
contains all the alternate repositories I'm using as git submodules. This
makes it easy to navigate the SD Card image used, along with any other
Agon projects. By default it's running the most upto date Console8
firmware (with minor not emulated fixes).

## Ideas Going Forward

So yes the `Terminal Mode` has quite extensive VT emulation. In a lot of ways
there is "common" code action. I do hope dead code elimination is used in the
compile as I have seen some "flashy but not WiFi stack and crypto protocol
free memory aware unused" code sections.

Maybe look over the bitmap and sprite code, but it is in flux. Extend the mode
7 functions with the 5 extra contol byte codes add some more simple but timbre
effective audio options.

Fix some more of those hindsight obvious logic bugs. Such as `0*x=0` in the
area of a bitmap (load bytes `&&`->`||`), and keycode up return (with held rollover).

It needs more thought, and integration of commits from "Official" and
"Console8" streams. The `vdp-gl` is also growing a little, and also it should
build against the emulator target.

Reducing some of the compile "purple zone" should also be a goal.
