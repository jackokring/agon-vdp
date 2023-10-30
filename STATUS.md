# Modding Status

The developmnet kit works on Arduino and so `linux-arm64` is fine for development.
My understanding is that if the MOS is not altered, and the "data tx/rx" remains
the same then there should be no problems flashing.

## Arduino IDE Loads and All is Well

The code is well structured, which is good. There is some minor initial confusion.

 * `FX` for doing things MOS side. Pun not intended. Best left alone for now.
  * Many of these "settings" have architecture specifics.
 * `VDU` basically a character echo with control sequences running on the VDP.
  * 0, 1, 2, 3, 6 and 27 
  * Some `VDU` initial control codes have no implemented meaning.
   * 0 nothing.
   * 1 send to printer only.
   * 2 print on.
   * 3 print off.
   * 6 enable screen.
   * 27 send next literal less than 32 to screen. Seems ripe for values greater than 32?
  * 23 is heavily overloaded on the following byte values below 32 as they have no UDG.
   * 0, 1, 7, 16, 27 and 28 are used when following a code 23.
   * 127 is also not a real UDG, but is likely a font glyph (unprinted), given that
   bitmaps are rectangular.
   
## The Emulator VDP

The emulator uses a customized VDP, so altered files can be
placed atop a copy of the quark104.

`cp ~/agon-vdp/video/? ~/fab-agon-emulator/src/vdp/vdp-quark666/?`

Where `?` is the modified file.

