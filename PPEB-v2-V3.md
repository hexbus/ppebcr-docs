
# PPEB v2 and v3 Board assembly

---

![V3 Board](/images/PPEB-V3.jpg)

--- 
## BOM
| Location | Value | Notes |
|----------|------|-------|
| J1 | 22x2P / 44 pin, 2.54mm / 0.1" 3A * 240-44 | (industry standard edge connector) |
| S1 | Surface Mount Micro SD Card Socket | (E.G. uxcell spring loaded Push/Push type)|
| U1 | Pi Pico W| |
| U2,U7 | AP6404L-3SQR-SN (tested),  ESP-PSRAM64H (tested), Lyontek LY68L6400 (untested) or IPUS IPS6404 (untested) | U2 Must Be installed, U7 optional on v3 boards for 8Mb Sams
| U3, U4, U5 | 74LVC245 | Sockets may be installed ( Dip 20 ) |
| U6 | 74HC138 | Sockets may be installed ( Dip 16 )|
| R1, R2, R3 | 10K Ohm | |
| R4 | 470 Ohm | The old board was a 10K pull-up, this is for the new version, and is now a current limiter on the SPI clock) |
| R5 | 560 Ohm | |
| R6 | 100 Ohm | |
| R7 | 1.8K Ohm | |
| R8 | 10K Ohm  | (V3 board) |
| Q1, Q2, Q3 | 2N7000 | |
| C1 | 1uF (bipolar) | |
| C2 | 100uF (bipolar - original spec was 47uF, but I had trouble getting small low voltage ones and used a 6V polarised one) | |
| C3 | 100nF (bipolar) | |

---

Building the board...  A small or fine soldering iron tip is required, often cleaned by quickly wiping on a folded tissue.  A flux pen will be invaluable.
Surface mount items should be done first, in this order:
- SD Card Socket, tin (pre-apply solder) to the pins ensuring any excess is removed, position the socket on the board and with a clean soldering tip apply heat to each pin to join.
- SD Card Socket, 4 corner pads - not pre-tinned, so they lie as flat as possible - can now be soldered down with flux-core-solder to secure the socket in place.
- 8MB PSRAM 8 pin chip - same as above, pre-tin (and remove excess) and with a clean iron tip and holding down the chip with a little pressure, join each pin to the board in turn.
- The Pi Pico W, position it and solder one corner pad in place, and while kept hot with the solding iron, manoeuvre it into precise position, then finish off all the other pins.
- All other components - they are easier.  Generally, do one "through hole" leg first, then while pressing down on the component, reheat it to settle it in place.  Finish the other pins.



# **Final Inspection**

- Verify:
  - All solder joints are clean.
  - No solder bridges between pins.
  - SD card socket functional.
- DO NOT power device until all checks complete.

# You are now ready to flash the PPEB firmware to the Pi Pico W.
---

 <br>
  <br>

## [Back to Main Guide](/README.md)

