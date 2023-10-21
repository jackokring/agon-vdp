# Modding Status

The developmnet kit works on Arduino and so `linux-arm64` is fine for development.
My understanding is that if the MOS is not altered, and the "data tx/rx" remains
the same then there should be no problems flashing.

## Arduino IDE Loads and All is Well

The code is well structured, which is good. There is some minor initial confusion.

 * `FX` for doing things MOS side. Pun not intended. Best left alone for now.
 * `VDU` basically a character echo with control sequences running on the VDP.
  * 0, 1, 2, 3, 6 and 27 `VDU` initial control codes have no implemented meaning.
  * 23 is heavily overloaded on the following byte values below 32.
   * 0, 1, 7, 16, 27 and 28 are used when following a code 23.

