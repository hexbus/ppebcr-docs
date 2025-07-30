# PPEB-CR SMD Board Assembly Guide

## [JLCPCB Gerber and Assembly files are available here ](https://github.com/dabonetn/ppeb-cr/tree/main/Kicad/production)

<br>


![PPEBcr Board Top Assembled](/images/PPEB-cr-Front.jpg)


<br>
<br>

# This assembly process is for the PPEB-cr (SMD preassembled) version

 The board should come with all the SMD components except the PSram if you order it assembled from JLCPCB.
 (Sometimes they may have the psram, but it has been out of stock)


## SMD Components (If you want to self assemble)
| Quantity | Size | Notes | Value | LCSC Part Number |
|----------|------|-------|--------|-----|
| 3 | SOT23 | Q1, Q2, Q3 | 2n7002 |C38141 |
| 4 | 0805 | R1,R2,R3,R8 | 10K | C2930231 |
| 2 | 0805 | R6,R9 | 100 | C17408 |
| 1 | 0805 | R4 | 470 | C2907329 |
| 1 | 0805 | R5 | 560 | C28636 |
| 1 | 0805 | R7 | 1.8k | C17398 |
| 2 | 0805 |C1,C8 | 1uf | C28323 |
| 5 | 0805 |C3,C5,C6,C7,C8 | .1uf (100nf) | C38141 |
| 1 | 1206 |C2 | 100uf | C883598 |
| 3 | TSSOP-20 | U3,U4,U5 | 74LVC245 | C6082 |
| 1 | SOIC-16 | U6 | 74HC138 | C6818 | 
| 2 | SOP8 | U2,U7 | APS6404L-3SQR-SN | C5333729 |
| 1 | MicroSD Slot | SDCARD1 | | C381084 |
---



## ⚠️ Before You Start

- Verify BOM and parts list — exact part sourcing may vary depending on vendor and regional availability.
- Work on an ESD-safe work surface.
- If unsure about SMD soldering, seek assistance.

---

## 🧰 Required Components

| Quantity | Part | Notes | Part Number |
|----------|------|-------|-------|
| 1 | PPEB3-SMD Latest Version | SMD preassembled |From provided KiCAD files |
| 1 | Raspberry Pi Pico W | Main processor | SC0918 |
| 2 | 8MB PSRAM  | 8MB or 16MB total (Up to 8MB usable on the TI)| APS6404L-3SQR-SN |
| 1 | Edge Connector (TI Sideport) | 44-pin edge connector | 5530843-4 |
| 1 | USB ( Optional)| Single Through Hole USB 2.0 Female Connector | GSB12121031EU |
| 1 | Reset Button (optional) | Standard 6x6x5 tactile switch | TS02-66-50-BK-100-LCR-D |

## 🧰 Optional
| Quantity | Part | Notes |
|----------|------|-------|
| 1 | 3D Printed Case | STL files provided |




## 🔧 Build Procedure


### 1️⃣ **Pico W Installation**

- Pico W module solders to the board using the castellated edges on the Pico.
- Verify correct alignment:
  - USB port facing front of board
  - Pin 1 correctly oriented
  - I usually use pin 20 as the first one to align the pico on the board.
  - Apply soldering iron to the pcb and the edge of the pico, and apply solder.

---

### 2️⃣ **PS Ram Installtion**

- PS Ram is a 8 Pin SOP , so surface mount soldering skills are needed.
- U2 is required to be installed for standard operation, and that will give you 2MB Sams memory.
- Install u7 for 8mb Sams Ram, or leave off if you are planning to add rs232 via GPIO 8 & 9.

### 3️⃣ **Sideport Edge Connector**

- Bend the pins on the edge connector in, using a flat surface like a table, they should look like a /\ with just enough room for the board to squeeze between the pins. (Tight is ok)
- Slide the board into the 44-pin sideport edge connector carefully.
- Check for bent pins, and try to align the board and edge connector in a straight line.  Think of a T wth the edge connector being the top.
- Solder the two opposite end pins on one side, then check to make sure the connector is straight with the board, then solder the other side. Then solder all the other connections on the connector.

---

### 4️⃣ **Reset Button (Optional)**

- Mount the reset button on the PCB header.
- Cut the 2 pins closest to the edge of the board flush before soldering. (To fit the case better)
- Solder the connectors.

---

### 5️⃣ **Rear USB Port (REQUIRED FOR REAR USB TO WORK!!!)**

- Install USB Connector
  - Solder the 4 usb pins and the 2 support pins.
  - Solder points tp2 and tp3 from the underside of the board to the underside of the pi pico. (This makes the actual USB connection to the rear)
  - Here's how it should look after install.
  
![PPEBcr Board Bottom Assembled](/images/PPEB-cr-Rear.jpg)  




--- 

### 6️⃣ **Remove C4 (Original SMD Board Revision Only)**

- If you have the original issue of the cr board, remove c4 (It's located near u7)

---
### 7️⃣ **Program the PICO**

- Before installing into the case, be sure to program the Pi Pico W. (See firmware instructions below)

---

### 8️⃣ **RS232 Breakout (Optional)**

- If RS232 functionality is desired:
  - Wire `GP8 → TX` and `GP9 → RX` to external header along with Gnd. Gnd is available on several pins of the pico.
  - Voltage is 3.3V logic-level — external level shifter required if interfacing to legacy DB9 serial hardware.

---


# **Final Inspection**

- Verify:
  - All solder joints are clean.
  - No solder bridges between pins.
  - SD card socket functional.
  - Case fits without stress on PCB.
- DO NOT power device until all checks complete.

# You are now ready to flash the PPEB firmware to the Pi Pico W.
---

 <br>
  <br>

## [Back to Main Guide](/README.md)


