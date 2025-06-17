# PPEB-cr (Pi Pico W Peripheral Expansion Box)  
*Complete Build & User Manual — TI-99/4A Sideport Expansion*

## *THIS IS DRAFT and I accept merge requests with corrections to fix mistakes.*

## 📖 Project Overview

[**PPEB-cr**](https://github.com/dabonetn/ppeb-cr) is a fully integrated, all-in-one expansion solution for the TI-99/4A home computer.  
Built around the Raspberry Pi Pico W, it recreates multiple expansion cards and peripherals inside one modern sideport device:

- Speech Synthesizer (PWM Emulated)
- 32K RAM Expansion
- SAMS Memory (1MB/8MB PSRAM)
- Myarc RAM (512K)
- Cartridge GROM/GRAM loader
- Disk Controller with .DSK sector image support
- IDE Hard Disk Emulation via USB stick
- RS232 Serial Support (3.3V or TCP/IP socket)
- TIPI Extensions Compatibility
- USB Keyboard, Mouse, Joystick, Bluetooth HID support
- Math Co-Processor for accelerated floating-point math
- Digi-Port 8-bit audio output support

The PPEB-cr project is built on the firmware by **JasonACT** with hardware modifications by **dabone**, community contributions via AtariAge, and extensive testing by numerous TI-99 enthusiasts.

This documentation provides a **turnkey build guide**, full feature reference, wiring diagrams, SD card prep instructions, configuration options, and troubleshooting information — designed for both builders and end-users.

---

## ⚠️ Safety Warnings

> **IMPORTANT — READ BEFORE USING OR BUILDING:**

- ⚠️ **Never power the PPEB-cr through USB while it is connected to your TI-99/4A.**  
  The device draws power directly from the TI sideport. USB power while inserted can cause hardware damage.
  
- ⚠️ **Always power down the TI-99/4A before inserting or removing the PPEB-cr.**  
  Repeated insertion/removal while powered on can damage TI-99 side port buffer chips.

- ⚠️ **The QI (Quality Improved) model of the TI-99/4A is not supported without hardware modifications.**  
  This is a TI motherboard limitation: QI units block GROM access on the side port, which is required for cartridge emulation.

---

## 🔧 Hardware Requirements

| Component | Description | Notes |
|-----------|-------------|-------|
| **PPEB-cr PCB** | v3.x Board Design | Provided as KiCAD/Gerber files |
| **Raspberry Pi Pico W** | Main processor | Must be Pico W version |
| **PSRAM Module** | 8MB (2x 4MB chips recommended) | Verified with memory tester |
| **MicroSD Card** | FAT32 formatted | 8GB or larger recommended |
| **SD Socket** | Surface-mount or vertical adapter | Mounted directly to PCB |
| **Reset Button** | Optional | Allows external reset |
| **Sideport Edge Connector** | TI-99/4A sideport interface | Keyed to TI-99 bus |
| **GPIO Headers** | As per PCB design | Standard 0.1" pitch headers |
| **Audio Output Jack** | Optional | For speech synthesizer audio |
| **USB Hub (optional)** | External powered hub | For keyboard, mouse, USB storage |
| **RS232 Wiring (optional)** | 3.3V logic level breakout | GP8 = TX, GP9 = RX |
| **3D Printed Case** | PPEB-cr Enclosure | STL files provided |

---

## 🔩 Special Build Notes

- 🛠 **C4 Capacitor Removal:**  
  On earlier boards (including some unbuilt kits), **C4 must be removed** for SD card compatibility.  
  This resolves voltage instability on certain SD card models.

- 🛠 **Speech Synthesizer Wiring:**  
  Audio output is produced via PWM from the Pico and routed through a passive RC filter to an audio jack.  
  This is optional but recommended for full functionality.

- 🛠 **RS232 Support:**  
  RS232 is provided via the Pico W's second UART at 3.3V logic levels.  
  If you require RS232 output, connect:
  - `GP8 → TX`
  - `GP9 → RX`

- 🛠 **Power Supply Stability:**  
  TI-99/4A power supply health is critical to PPEB-cr stability.  
  Many random lockups have been traced to marginal regulators or aged power bricks.  
  If experiencing intermittent behavior, verify or recap your TI power supply.

---

## 📦 What This Manual Provides

- ✅ Full Build Guide  
- ✅ SD Card Setup  
- ✅ Firmware Installation  
- ✅ Configuration File Reference (`autoload.cfg`)  
- ✅ TI BASIC Command Reference (`CALL TIPI`, `CALL EXPON`, etc.)  
- ✅ IDE and USB Storage Setup  
- ✅ Advanced Feature Documentation  
- ✅ Troubleshooting Guide  
- ✅ STL Case Printing Instructions  
- ✅ Appendices: Full Config Option Table & Memory Tester Usage

---

> 🚀 **Next Section:** [Assembly Instructions →](#assembly-instructions)

---
