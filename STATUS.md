# Modding Status

The developmnet kit works on Arduino and so `linux-arm64` is fine for development.
My understanding is that if the MOS is not altered, and the "data tx/rx" remains
the same then there should be no problems flashing.

## Arduino IDE Loads and All is Well (v6.66 is as good a number as any)

The code is well structured, which is good. There is some minor initial confusion.

 * `FX` for doing things MOS side. Pun not intended. Best left alone for now.
 Many of these "settings" have architecture specifics.
 * `VDU` basically a character echo with control sequences running on the VDP. 
 Some `VDU` initial control codes have no implemented meaning.
   * **0** nothing. So should remain fixed.
   * 1 send to printer only.
   * 2 print on.
   * 3 print off.
   * 6 enable screen.
   * **27** send next literal less than 32 to screen.
     * I've requesitioned this one.
   * 23 is heavily overloaded on the following byte values below 32 as they
   have no UDG.
     * **127** is also not a real UDG, but is likely a font glyph (unprinted),
     given that bitmaps are rectangular. This is perhaps the ideal intercept.
 * `Mode 7` codes 128+ (0, 14, 15, 16, 27) technically free control codes.
   * The Box on/off codes are not used either (10, 11).
   * Apparently I might do 24 instead of 25 lines.
     * I think it might encourage some restriction thinking.
   * There's maybe an open mode number for double buffered `Mode 135`.
   * Chopping out the unreferenced parts of the font and noticing height is
   less than 500 always seems to save about 1% of the flash space.
   This might be useful later with trimming the 20th row and `<WiFi.h>`.

## The Emulator VDP

The emulator uses a customized VDP, so altered files can be
placed atop a copy of the quark104.

`cp ~/agon-vdp/video/? ~/fab-agon-emulator/src/vdp/vdp-quark666/?`

Where `?` is the modified file.

