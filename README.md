# PPEB (formal name: Pi Pico W Peripheral Expansion Box Side Port Device)  
*Complete Build & User Manual — TI-99/4A Sideport Expansion*

## *THIS IS DRAFT and I accept pull/merge requests with corrections to fix mistakes.*

> *(Click any section below to jump directly to that part of the manual.)*
# 📑 Table of Contents

- [Project Overview](#project-overview)  
- [Safety Warnings](#safety-warnings)
- [What This Manual Provides](#what-this-manual-provides)
- [Assembly Guides](#AssemblyGuides)
- [Firmware Installation](#firmware-installation)
- [SD Card Preparation](#sd-card-preparation)
- [Configuration File Reference (`autoload.cfg`)](#configuration-file-reference-autoloadcfg)
- [TI BASIC Commands Reference](#ti-basic-commands-reference)
- [IDE and USB Storage Setup](#ide-and-usb-storage-setup)
- [Advanced Features](#advanced-features)
- [Troubleshooting Guide](#troubleshooting-guide)
- [Case Printing Instructions](#case-printing-instructions)
- [Appendix A — Full Config Parameter Table](#appendix-a--full-config-parameter-table)
- [Appendix B — Memory Tester Usage](#appendix-b--memory-tester-usage)
- [Credits and Contributors](#credits-and-contributors)


## Project Overview <a name="project-overview"></a>

---

[**PPEB**] is a fully integrated, all-in-one expansion solution for the TI-99/4A home computer.  
Built around the Raspberry Pi Pico W, it recreates multiple expansion cards and peripherals inside one modern sideport device:

- Speech Synthesizer (PWM Emulated)
- 32K RAM Expansion
- SAMS Memory (2MB/8MB PSRAM)
- Myarc RAM (512K)
- Cartridge GROM/GRAM loader
- Disk Controller with .DSK sector image support
- IDE Hard Disk Emulation via USB stick
- RS232 Serial Support (3.3V or TCP/IP socket)
- TIPI Extensions Compatibility
- USB Keyboard, Mouse, Joystick, Bluetooth HID support
- Math Co-Processor for accelerated floating-point math
- Digi-Port 8-bit audio output support
- RS232 Support is provided via the Pico W's second UART at 3.3V logic levels. Not compatible with 8MB memory for Sams.
  
<br>

The PPEB project is built on the firmware and original hardware by **JasonACT** with hardware modifications by **dabone**, documentation by **hexbus**, community contributions via AtariAge, and extensive testing by numerous TI-99 enthusiasts.

---

## Safety Warnings <a name="safety-warnings"></a>

> **IMPORTANT — READ BEFORE USING OR BUILDING:**

- ⚠️ **Never power the PPEB through USB while it is connected to your TI-99/4A.**  
  The device draws power directly from the TI sideport. USB power while inserted can cause hardware damage.
  
- ⚠️ **Always power down the TI-99/4A before inserting or removing the PPEB.**  
  Repeated insertion/removal while powered on can damage TI-99 side port buffer chips.

- ⚠️ **The QI (Quality Improved) model of the TI-99/4A is not supported without hardware modifications.**  
  This is a TI motherboard limitation: QI units block GROM access on the side port, which is required for cartridge emulation.



- 🛠 **Power Supply Stability:**  
  TI-99/4A power supply health is critical to PPEB stability.  
  Many random lockups have been traced to marginal regulators or aged power bricks.  
  If experiencing intermittent behavior, verify or recap your TI power supply.



---


## What This Manual Provides <a name="what-this-manual-provides"></a>

- ✅ Assembly Guides
- ✅ 3D Printable Case Files 
- ✅ SD Card Setup  
- ✅ Firmware Installation  
- ✅ Configuration File Reference (`autoload.cfg`)  
- ✅ TI BASIC Command Reference (`CALL TIPI`, `CALL EXPON`, etc.)  
- ✅ IDE and USB Storage Setup  
- ✅ Advanced Feature Documentation  
- ✅ Troubleshooting Guide  
- ✅ Appendices: Full Config Option Table & Memory Tester Usage



## Hardware 

There are 3 main versions of the PPEB.

The original version with one PSRam (2Mb Sams)
The updated v3 board with dual PSRam (8Mb Sams)
The SMD version with dual PSRam (8mb Sams), reset button, and rear mounted MicroSd and USB Ports.  

## Assembly Guides <a name="AssemblyGuides"></a>

Here you will find the guides for the different versions of the PPEB pcbs.

[Click here for PPEB Version 2 & 3 Assembly guide](/PPEB-v2-V3.md)
<br>
[Click here for PPEB-cr SMD Version Assembly guide](/PPEBCR.md)

---

# Firmware Installation <a name="firmware-installation"></a>

---

## 📋 Overview

The PPEB uses custom firmware built for the Raspberry Pi Pico W, created by JasonACT and the AtariAge development community.

The firmware package includes:

- Main firmware UF2 image
- ROM packs
- DSR files
- Support files for SD card structure

You only need to flash the UF2 file once to load the firmware onto the Pico W.  
Future updates may simply involve replacing files on the MicroSD card.

---

## 🗃 Firmware Package Contents

[Latest beta firmware download here.](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/35/#findComment-5680418)

Download the `PPEB2.zip` unzip it, and you should have:

| File / Folder | Purpose |
|----------------|---------|
| `PPEB2_2M.ino.uf2` | Main firmware binary for 2MB Version (Single PSRam Chip) |
| `PPEB2_8M.ino.uf2` | Main firmware binary for 8MB Version (Dual PSRam Chip) |
| `/ROMS/` | ROM, Bins, and Blank disk files |

---

## 🖥 Flashing Procedure

> **⚠ NOTE:** The Pico W must be flashed BEFORE inserting it into the TI-99/4A system!

### 1️⃣ Connect Pico W

- Do not have a Microsd card inserted.
- Hold down the **BOOTSEL** button on your Pico W.
- While holding the button, connect the Pico to your PC using a microUSB cable.
- The Pico should appear as a USB mass storage device named **RPI-RP2**.

### 2️⃣ Flash UF2 Firmware

- Pick the Correct Firmware for your device - If you have 1 PSRam, then select the 2mb version, 2 PSRams, select the 8mb version.
- Copy the file `PPEB.uf2` directly onto the Pico’s USB drive.  These files are only [located on AtariAge](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/28/#findComment-5639111).
- After the file copies, the Pico will automatically reboot into PPEB mode.
- It should blink 3 times when powered with no microsd inserted.
- It should blink once when powered on with a microsd card with the correct files is inserted.

> ✅ Firmware is now installed and ready for use.

---

## 🔄 Future Firmware Updates

- To update firmware:
  - Repeat this same UF2 flashing procedure.
  - No special erasure or prep needed.
- Updating firmware will not affect SD card contents or existing configuration.

---

## 🛑 Important Reminder

- NEVER power the Pico via USB when installed in the TI-99/4A sideport.
- All power should come from the TI sideport itself.

---

> ✅ Firmware installed. Proceed to [SD Card Preparation →](#sd-card-preparation)

---
# SD Card Preparation <a name="sd-card-preparation"></a>

---

## 📋 Overview

The MicroSD card is absolutely essential to PPEB operation.  
The device will not function properly without a properly prepared SD card inserted.

This section provides a complete guide to:

- SD card formatting
- File structure
- File placement
- Common pitfalls

---

## 📂 Supported Card Types

- Format: **FAT32 (MBR partitioning only)**
- Size: 8GB to 32GB recommended.
- Brand: Name-brand cards strongly recommended for compatibility (SanDisk, Samsung, Kingston, etc.).

---

## ⚠️ DO NOT USE:

- exFAT format
- GPT partition tables
- Cards with manufacturer "quick formats"
- SDXC cards larger than 32GB (unless specially pre-formatted for FAT32)

---

## 🧹 Clean Formatting (Highly Recommended)

### Windows (`diskpart` method)

1. Open **Command Prompt** (as Administrator)
2. Type:
    ```
    diskpart
    list disk
    select disk # (your SD card number)
    clean
    create partition primary
    format fs=fat32 quick
    assign
    exit
    ```

### macOS/Linux

- Use **Disk Utility** or `gparted`:
  - Partition table: MBR (Master Boot Record)
  - Format: MS-DOS FAT32

---

## 📂 SD Card File Structure

Be sure to get the matching DSR.ROM that matches your firmware. Or just download the latest and reflash your PPEB.

[Latest beta firmware download here.](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/35/#findComment-5680418)


Your SD card root should contain the at least the following folders and files.  
These files are in the firmware package in the roms directory.  
(You will have to create your own autoload.cfg of download the sample card image below that has a barebones file in it)

The easiest way to do this is just copy the content of the roms directory to the root of your sd card.

```
/
├── autoload.cfg           <-- Main configuration file
├── DSR.ROM                <-- MAIN DSR.ROM (REQUIRED!!!)
├── DISK.ROM               <-- Needed for Disk Emulation. 
├── PICOPEBG.BIN           <-- This the PPEBMenu that can be autostarted with the cart option in autoload.cfg

Optional

├── PCode.GRM              <-- Needed for PCODE Card Emulation
├── PCode.ROM              <-- Needed for PCODE Card Emulation
├── BLNK.DSK               <-- Blank Disk Image

```

> NOTE THAT P-CODE files must reside in the **root** of the SD card if you wish to use PCode mode.


 A sample card image can be found here. [Download Sample Card Image](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/28/#findComment-5616188).

Just unzip it to the root of your sdcard. If you have done this correctly you will have the following also appear in the root of the sdcard.



```
/
├── autoload.cfg           <-- Main configuration file
├── BLNK.DSK               <-- Blank RAM disk image (optional)
├── Development            <-- Programming Tools ROMs folder
├── Educational            <-- Educational Programs and Games folder
├── Games                  <-- Games folder
├── Homebrew               <-- Homebrew Games
└── Tools                  <-- TI Tools (Disk Manager, etc) folder
```


---

## 📂 Sample `autoload.cfg` 

A basic starting configuration file may look like this:

```
CRU=1
PCODE=0
BAUD=9600
WIFI=YourSSID
PASS=YourWiFiPassword
SNTP=pool.ntp.org
TZHR=-5
TZMN=0
D1MAP=/DSK1/BLNK.DSK
CART=/PICOPEB
RESET=1
```

> A full list of all supported configuration fields will be documented in the [Configuration Reference](#configuration-reference).

---

## 🧪 Validate Card Preparation

Before first use:

- Insert SD card into the fully assembled PPEB unit.
- Apply power to TI-99/4A.
- If `autoload.cfg` is valid, the PPEB will initialize automatically.
- Improper SD formatting is one of the most common causes of boot failure.

---

# Configuration File Reference (`autoload.cfg`) <a name="configuration-file-reference-autoloadcfg"></a>

---

## 📋 Overview

The `autoload.cfg` file controls nearly all PPEB operating parameters.  
It is read at startup from the **root directory** of the SD card.

This section documents **all supported configuration fields**, derived directly from the firmware source (`PPEB2.ino`) and AtariAge thread discussions.
___
 
**Automatic Loading of PPEB Menu from the TI Boot Screen**

If you want your Pi Pico PEB to boot with a menu, be sure to get the PICOPEBG.BIN from the firmware pack in the ROMS directory
<br>Unzip it and put it on your sd Card. And place the following in your autoload.cfg

```ini
CART=/PICOPEB
```
If you have the reset button installed or a PPEB-cr version, then also add RESET=1 to allow returning to the PPEB menu with the reset button.
(I think this is incompatible with a rs232 adapter installed,
<br> I've not tested the rs232 function)




---

### 📂 File Format

- File name: `autoload.cfg`
- Format: Plain text (ASCII)
- One setting per line: `KEY=VALUE`
- No quotes needed around values
- Blank lines and comment lines (starting with `#`) are allowed

Example:

```ini
CRU=1
WIFI=MyWiFi
PASS=SuperSecret
SNTP=pool.ntp.org
```

---

## 🖧 System Configuration

| Parameter | Description | Example |
|-----------|-------------|---------|
| `CRU` | CRU base offset for DSR (TI-99 sideport address base) | `CRU=1` (→1100h) |
| `BAUD` | RS232 baud rate override | `BAUD=9600` |
| `WIFI` | WiFi SSID name | `WIFI=YourSSID` |
| `PASS` | WiFi password | `PASS=YourPassword` |
| `SNTP` | Time server (NTP hostname) | `SNTP=pool.ntp.org` |
| `TZHR` | Timezone offset (hours) | `TZHR=-5` |
| `TZMN` | Timezone offset (minutes) | `TZMN=0` |
| `TMFT` | Date format: `0`=US, `1`=AU | `TMFT=0` |

---

## 💾 File System & Drive Mapping

| Parameter | Description | Example |
|-----------|-------------|---------|
| `D1MAP` → `D9MAP` | Map DSK folders or images | `D1MAP=/DSK1/DISKNAME.DSK` |
| `MAP1` → `MAP9` | (Alternative mapping commands) | Need to verify |
| `C1MAP` | Map CS1 file | `C1MAP=/CS1/file` |
| `C2MAP` | Map CS2 file | `C2MAP=/CS2/file` |
| `CART` | Cartridge image to autoload | `CART=MyCart` |
| `CART2`, `CART3` | Additional multi-bank cartridges | `CART2=AnotherCart` |
| `USBD` | USB stick override for DSK mapping | `USBD=1` (sets DSK1 to USB) |

---

## 🧮 Memory & Emulation Settings

| Parameter | Description | Example |
|-----------|-------------|---------|
| `MMEM` | Enable Mini-Memory RAM | `MMEM=1` |
| `MMMD` | Mini-Memory mode: `0`=8KB, `1`=4KB | `MMMD=0` |
| `PCODE` | Enable PCode Card emulation | `PCODE=1` |
| `MYARC` | Enable Myarc RAM emulation (512K) | `MYARC=1` |
| `RAMBO` | Enable SAMS RAM at >4000 when DSR ROM disabled | `RAMBO=1` |
| `PLAYT` | PlayThing ROM/RAM enable | `PLAYT=1` |

---

## 🎛 Performance Timing Controls

| Parameter | Description | Example |
|-----------|-------------|---------|
| `MHZ` | Pico CPU speed (even values `250`–`280`) | `MHZ=258` |
| `NOPS` | Read delay cycles (`1`–`9`) | `NOPS=5` |
| `NOPSW` | Write delay cycles (`1`–`9`) | `NOPSW=5` |

> ⚠ Use default timing values unless specifically tuning for unstable PSRAM behavior.

---

## 🎤 Audio & Speech Options

| Parameter | Description | Example |
|-----------|-------------|---------|
| `PSNDO` | Enable Digi-Port 8-bit audio | `PSNDO=1` |
| `FORTI` | ForTI sound card emulation (`0`=off, `1-3` modes) | `FORTI=1` | 
| `FORTI=1` | one chip emulated    - plays left & right channel in mono the same as the TI | `FORTI=1` | 
| `FORTI=2` | 4 chips emulated - plays in stereo, | `FORTI=2` | 
| `FORTI=3` | 4 chips emulated - plays in mono | `FORTI=3` | 

Forti Sound is ONLY availble using a Bluetooth Speaker, it is not mixed into the onboard sound of the TI.

---

## 🕹 Bluetooth Device Bindings

| Parameter | Description | Example |
|-----------|-------------|---------|
| `BTJ1` | Bluetooth Joystick 1 MAC address | `BTJ1=01:23:45:67:89:AB` |
| `BTJ2` | Bluetooth Joystick 2 MAC address | `BTJ2=CD:EF:12:34:56:78` |
| `BTKM1` | Bluetooth Keyboard MAC | `BTKM1=01:23:45:67:89:AB` |
| `BTKM2` | Bluetooth Mouse MAC | `BTKM2=CD:EF:12:34:56:78` |
| `BT5KB` | 5-key Bluetooth keyboard MAC | `BT5KB=01:23:45:67:89:AB` |
| `BTSPK` | Bluetooth speaker MAC | `BTSPK=01:23:45:67:89:AB` |

> ⚠ MAC addresses are specified in hex notation (uppercase, colon-separated).

---

## 💽 IDE Hard Disk Emulation

| Parameter | Description | Example |
|-----------|-------------|---------|
| `IDE` | Enable IDE emulation | `IDE=1` |
| `HARDDISK1` | First IDE image file | `HARDDISK1=harddisk1.dat` |
| `HARDDISK2` | Second IDE image file | `HARDDISK2=harddisk2.dat` |

- Image files must be placed on a connected USB drive (not SD card).

---

## 🖧 TIPI Extension & TCP/IP Options

| Parameter | Description | Example |
|-----------|-------------|---------|
| `PI.CLOCK` | Enables SNTP time setting | `PI.CLOCK=1` |
| `PI.TCP` | Client-only TCP socket | *(Reserved for advanced users)* |
| `SOCI` | Socket-to-keyboard interface | *(Reserved for advanced users)* |

---

## 📝 Notes:

- Missing or invalid fields will be ignored at startup.
- You may safely omit any setting not relevant to your build.
- Always ensure correct file paths match actual SD card folder names.

---

# TI BASIC Commands Reference <a name="ti-basic-commands-reference"></a>

---

## 📋 Overview

The PPEB adds numerous device service routines (DSRs) and control commands accessible directly from TI BASIC, Extended BASIC, and Assembly programs.

This section documents all supported `CALL` commands.

---

## 🖥 Storage & TIPI Commands

| Command | Description |
|---------|-------------|
| `CALL TIPI` | Launch TIPI menu browser — allows loading cartridges, modules, and configurations. |
| `CALL TIPI("/cartname")` | Directly load a cartridge ROM or GROM image from SD card. |
| `CALL UNMOUNT` | Cleanly unmount SD card before removal (remounts automatically on next access). |

---

## 💾 Disk Image Mapping

| Command | Description |
|---------|-------------|
| `CALL MAP1("/folder")` | Map `/folder` as DSK1. |
| `CALL MAP2("/folder")` ... `MAP9` | Map additional disk folders to DSK2–DSK9. |
| `CALL MAP1("/file.DSK")` | Map sector dump disk image directly as DSK1. |
| `CALL FLUSH(1)` | Write back RAM-cached disk changes to file for DSK1 (or other number). |

> RAM disk caching modes allow fast sector access with deferred writeback via `FLUSH()`.

---

## 📼 Cassette Image Mapping

| Command | Description |
|---------|-------------|
| `CALL MAPCS1("/file")` | Map TIFILES file as CS1 (cassette input). |
| `CALL MAPCS2("/file")` | Map TIFILES file as CS2. |

---

## 📋 File Operations

| Command | Description |
|---------|-------------|
| `CALL FILES(n)` | List files for DSKn device. Only supported if CRU address is >1100. |
| `CALL PASTE("/file")` | Send contents of file as simulated keyboard input via CRU interface. Use `:` suffix for manual F10 start. |

---

## 🧮 Memory Expansion Controls

| Command | Description |
|---------|-------------|
| `CALL EXPON` | Enable 32KB expansion RAM. |
| `CALL EXPOFF` | Disable 32KB expansion RAM. |
| `CALL CARTON` | Enable cartridge GRAM space. |
| `CALL CARTOFF` | Disable cartridge GRAM. |
| `CALL CARTMMO` | Enable 8KB/4KB MiniMemory RAM. |
| `CALL CARTMMF` | Disable MiniMemory RAM mode. |
| `CALL MYARCON` | Enable Myarc 512KB RAM mode. |
| `CALL MYARCOF` | Disable Myarc RAM mode. |

---

## 📊 Co-Processor Controls

| Command | Description |
|---------|-------------|
| `CALL MATHON` | Enable math co-processor acceleration (Radix-100 float ops). |
| `CALL MATHOFF` | Disable math co-processor. |

---

## 🖧 PCode Card Emulation

| Command | Description |
|---------|-------------|
| `CALL PCODEON` | Enable PCode Card mode. TI reboots into PCode OS. |
| `CALL PCODEOF` | Disable PCode Card emulation. |

> ⚠ Use `H` (halt) from PCode menu before disabling.

---

## 🕹 Joystick Mode Controls

| Command | Description |
|---------|-------------|
| `CALL JOY0` | Joystick mode 0: All directions allowed (Alpiner style). |
| `CALL JOY1` | Joystick mode 1: Locked to 4 directions, newest takes priority (Munchman style). |
| `CALL JOY2` | Joystick mode 2: Locked to 4 directions, oldest takes priority (Parsec style). |
| `CALL JOYOFF` | Disable joystick CRU interface (hands full control to TIPI extensions). |

---

## 📝 Notes

- Commands are case-insensitive but traditionally written uppercase.
- All commands may be entered directly from TI BASIC or Extended BASIC.
- These DSR calls control hardware features dynamically without reboot.

---

# IDE and USB Storage Setup <a name="ide-and-usb-storage-setup"></a>

---

## 📋 Overview

The PPEB supports IDE hard disk emulation through the use of USB storage devices.  
Unlike floppy disk image support (which uses the MicroSD), IDE emulation works **only via USB**.

The IDE emulation replicates Myarc-style hard disk functionality for compatible TI disk managers and OS environments.

---

## 🖧 Hardware Notes

- IDE images must be stored on a USB memory stick attached to the PPEB.
- The Pico W enumerates the USB drive automatically on startup.
- You may use a powered USB hub to allow multiple USB devices simultaneously (keyboard, mouse, joystick, USB drive).

---

## 📂 File Structure on USB

The USB stick root directory should contain:

```bash
/
├── harddisk1.dat      <-- IDE drive 1 image
├── harddisk2.dat      <-- IDE drive 2 image (optional)
```

- Image files are raw sector dumps, not filesystem dumps.
- File sizes up to ~2GB per image supported.
- Images may contain multiple partitions, formatted using Myarc HD utilities.

---

## 🔧 Enabling IDE in `autoload.cfg`

Example configuration:

```ini
IDE=1
HARDDISK1=harddisk1.dat
HARDDISK2=harddisk2.dat
```

- `IDE=1` activates IDE functionality.
- Filenames must match exactly (case-sensitive).

---

## 🧰 IDE Image Preparation

- IDE images may be created via:

  - Myarc utilities (real hardware or emulator)
  - MAME or Classic99 IDE tools
  - Raw image creation utilities (Linux `dd` or Windows disk image tools)

- Images may contain one or more TI partitions depending on tool used.

- It is strongly recommended to build IDE images on emulator or PC before transferring to USB.

- [Link to Hexbus's IDE Guide](https://hexbus.com/ti99geek/Projects/idedsr/idedsr.html#idedsr)

---

## ⚠ Common Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| IDE not detected | USB drive not seen | Try different hub port |
| Hard disk not formatted | Blank image used | Use Myarc utilities to initialize |
| Image too large | Unsupported file size | Keep images ≤ 2GB |

- Some USB hubs enumerate devices in varying orders — try swapping ports if devices conflict.
- Always eject USB drive safely from PC before inserting into PPEB.

---

## 📝 Notes

- The IDE interface works fully in conjunction with TIPI, SAMS, Myarc RAM, and DSR functions.
- Only one USB memory stick may be used for IDE storage per session.
- The USB drive is hot-plug capable only if properly unmounted beforehand.

---

# Advanced Features <a name="advanced-features"></a>

---

## 📋 Overview

The PPEB replicates — and in many cases expands — multiple classic TI expansion subsystems inside one sideport device.

This section summarizes each advanced subsystem available once fully configured.

---

## 🔧 SAMS Memory Expansion

- Fully supports 1MB or 8MB SAMS configurations.
- Uses external PSRAM modules connected to Pico W.
- Activated automatically when present.
- SAMS memory functions as expanded RAM for compatible software.
- Works with Geneve emulation, FinalGROM99, RXB, Myarc DOS, UCSD PCode, etc.

---

## 🔧 Myarc RAM Emulation (512KB)

- Emulates Myarc 512KB RAM card for compatible software.
- Swaps SAMS space dynamically to operate within Myarc RAM specifications.
- Controlled by:

```ini
MYARC=1
```

- May be toggled dynamically via:

```basic
CALL MYARCON
CALL MYARCOF
```

---

## 🔧 PlayThing ROM/RAM

- Experimental ROM mapping feature.
- Controlled by:

```ini
PLAYT=1
```

---

## 🔧 RAMBO Mode (SAMS at >4000)

- Enables SAMS mapping starting at address >4000h.
- Useful when no DSR ROM is active.
- Controlled by:

```ini
RAMBO=1
```

---

## 🔧 PCode Card Emulation

- Fully emulates TI's UCSD Pascal P-Code card.
- Requires:

  - `PCode.ROM` (12KB)  
  - `PCode.GRM` (62KB/64KB)

- Controlled by:

```ini
PCODE=1
```

- Dynamically enabled via:

```basic
CALL PCODEON
CALL PCODEOF
```

- ⚠ Always use `H` (Halt) in PCode OS before disabling.

---

## 🔧 TIPI Extensions

- Partial TIPI extensions are supported:
  - File system access via `CALL TIPI`.
  - Network time synchronization (`SNTP`, `PI.CLOCK`).
  - Partial mouse extensions.
  - TCP/IP socket server (`RS232/2`) and client.

> ⚠ Not a full TIPI replacement — designed as an integration layer.

---

## 🔧 RS232 Serial Interfaces

### Option 1 — Physical RS232 (3.3V)

- Available via Pico's UART2 interface:
  - `GP8 → TX`
  - `GP9 → RX`

- Logic-level (3.3V) only — use external level shifter for RS232 compliance.

- Baud rate set via:

```ini
BAUD=9600
```

### Option 2 — TCP/IP Socket RS232

- Virtual serial via WiFi over port `2322`.

- Accessed as:

```bash
RS232/2
```

- Allows remote terminal access using modern PC terminal programs.

---

## 🔧 Math Co-Processor

- Implements accelerated floating-point math using Radix-100 operations.
- Provides faster Add/Subtract/Multiply/Divide.
- Controlled dynamically via:

```basic
CALL MATHON
CALL MATHOFF
```

---

## 🔧 Speech Synthesizer (PWM Emulation)

- Fully emulates TI Speech Synthesizer using PWM audio on Pico GPIO.

- Audio output routing:

  1. Pico PWM → RC Filter (2.2kΩ + 0.1µF)
  2. Filter → Audio Jack output.

- Matches TI Speech DSR functionality for software compatibility.

- PWM signal quality depends on:

  - Pico system load.
  - Filtering circuit stability.

> ⚠ Omitting audio jack has no impact on firmware functionality if speech output not desired.

---

## 🔧 Digi-Port 8-bit Audio

- Provides additional PCM-style audio output.

- Controlled via:

```ini
PSNDO=1
```

- Output routed via PWM circuit alongside speech output.

---

## 🔧 ForTI Sound Card Emulation

- Emulates multi-chip TI ForTI sound expansion:

```ini
FORTI=1  (or 2 or 3)
```

| Mode | Description |
|------|-------------|
| `1` | One chip, mono output |
| `2` | All 4 chips, stereo output |
| `3` | All 4 chips, mono mixdown |

---

## 🔧 Bluetooth HID Device Support

- Supports:

  - Joysticks (`BTJ1`, `BTJ2`)
  - Keyboards (`BTKM1`)
  - Mice (`BTKM2`)
  - 5-Key Keyboards (`BT5KB`)
  - Bluetooth Speakers (`BTSPK`)

- MAC addresses assigned in config:

```ini
BTJ1=01:23:45:67:89:AB
```

- Devices must be paired externally (e.g. via Windows/Linux) to obtain MAC.

- Once bound, PPEB reconnects automatically at startup.

---

# Troubleshooting Guide <a name="troubleshooting-guide"></a>

---

## 📋 Overview

This section summarizes common problems, their likely causes, and solutions based on real-world PPEB builder reports.

---

## 🔌 Power & Stability Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Random lockups or resets | Weak TI-99/4A power supply | Replace or rebuild power supply; verify voltage stability. |
| Frequent freezes when loading ROMs | PSRAM instability | Use `memtest2.uf2` to verify PSRAM stability. Adjust `MHZ`, `NOPS`, and `NOPSW` in `autoload.cfg`. |
| Board not powering on | Faulty sideport edge connector or bad solder joints | Inspect all PCB solder joints, verify Pico power pins. |

---

## 💽 SD Card Problems

| Symptom | Cause | Solution |
|---------|-------|----------|
| PPEB not detected at all | Missing SD card or unreadable card | Verify card inserted; reformat as FAT32 (MBR). |
| Autoload failing | Corrupt or missing `autoload.cfg` | Verify syntax, file format (plain text). |
| File not found errors | Incorrect directory structure | Verify SD card file/folder layout matches documentation. |
| Random SD read failures | C4 capacitor still installed | **Remove C4** capacitor on PCB for full SD compatibility. |

---

## 🖧 WiFi / Time Sync Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| No clock sync | Incorrect WiFi config | Double-check `WIFI` and `PASS` in `autoload.cfg`. |
| Fails to connect | Weak WiFi signal | Test closer to router; ensure WPA2 compatibility. |

---

## 💾 IDE Storage Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| IDE not recognized | USB stick not detected | Use name-brand USB stick; verify hub connection order. |
| Drive not formatted | Blank image | Use Myarc tools to partition & format images beforehand. |
| Image won't load | Image file too large | Stay below 2GB per image file. |

---

## 🔊 Speech Synth / Audio Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Distorted speech | PWM artifacts | Verify RC filter installed correctly. |
| No audio output | No jack wired | Install optional audio output circuit. |

---

## 🎮 USB & Bluetooth Device Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| USB devices not detected | Hub order issue | Use powered hub; verify enumeration order. |
| Bluetooth device won't pair | Incorrect MAC address | Verify pairing on PC first; enter correct MAC into config. |
| Input lag or dropped presses | Heavy system load | Limit concurrent active features for best responsiveness. |

---

## 🔧 Firmware Flashing Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| Pico won't enter USB mode | Incorrect BOOTSEL usage | Hold BOOTSEL while connecting USB. |
| Firmware not flashing | Wrong UF2 file | Use proper `PPEB.uf2` firmware file provided. |

---

## 📦 Case Fitment Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| PCB lifts inside case | Reset pin height too tall | Use spacers under PCB or trim reset pin height. |
| Case won't close | Misaligned edge connector | Verify sideport connector alignment during assembly. |

---

# Case Printing Instructions <a name="case-printing-instructions"></a>

---

## 📋 Overview

The PPEB hardware design includes printable STL files for a full enclosure, suitable for both home FDM printers and commercial 3D print services.

The case design protects the PCB, provides proper mounting holes, and exposes:

- TI-99/4A sideport connector
- MicroSD slot
- Reset button (optional)
- USB port
- Audio output jack (optional)

---

## 🧰 Provided STL Files

## V2/V3 Version By JasonACT

[Click Here](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/35/#findComment-5680281)


### PPEBcr (SMD Version)

**dabone (Mark Ormond / dabonetn)**  
<br>
 Github Link 
<br>
[Click Here](https://github.com/dabonetn/ppeb-cr/tree/main/STLs)
<br>

**PPZ-00**

STL file design for printable enclosures  
[Atariage Link](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/30/#findComment-5653770)
  

## 🖨 Recommended Print Settings

| Setting | Value |
|---------|-------|
| Layer Height | 0.2mm |
| Infill | 20% |
| Supports | Not required (self-supporting design) |
| Material | PLA, PETG, or ASA |
| Print Orientation | Flat on bed, face-down recommended |
| Wall Count | 3+ walls for durability |
| Supports | Not Needed for most printers |



---

## 🔩 Assembly Hardware

|Version| Part | Quantity | |
|------|----------|-----|----|
| dabonetn | M3x8 or M3x10 | 3 |
| dabonetn | M3 Nuts (or inserts)| 3 | 
| dabonetn | M3 Insert (or nuts) | 3 |
| PPZ-00 | M3x10mm Screws | 4 (for securing PCB to case posts) | Self-tapping M3 screws will thread directly into printed holes. Use moderate torque to avoid splitting posts.|
| PPZ-00 | Optional rubber feet | 4 |


---

## 🔧 Assembly Procedure

1. Verify PCB fully assembled and tested.
2. Insert PCB into bottom case half.
3. Place button in hole on top case
4. Place halves together and screw together.
---

## ⚠ Known Fitment Notes

- Reset button stem may protrude slightly if using taller tact switches.
- Allow ~1.5mm of vertical headroom for reset switch clearance.
- Case tolerances adjusted for v3.2a board revision.
- Slight case sanding may improve final fit depending on printer calibration.

---

# Appendix A — Full Config Parameter Table <a name="appendix-a--full-config-parameter-table"></a>

---

| Parameter | Value Type | Description | Default |
|-----------|------------|-------------|---------|
| `CRU` | Integer (Hex Digit 0–D) | DSR CRU Base Offset (×100h) | 1 |
| `BAUD` | Integer | RS232 baud rate | 9600 |
| `WIFI` | String | WiFi SSID | *(none)* |
| `PASS` | String | WiFi password | *(none)* |
| `SNTP` | String | NTP time server | *(none)* |
| `TZHR` | Integer | Timezone offset (hours) | 0 |
| `TZMN` | Integer | Timezone offset (minutes) | 0 |
| `TMFT` | 0 or 1 | Date format: 0=US, 1=AU | 0 |
| `DSK1`–`DSK9` | Path | Map directories or disk images | *(none)* |
| `MAP1`–`MAP9` | Path | Alternative disk mappings | *(none)* |
| `C1MAP` | Path | Map CS1 file | *(none)* |
| `C2MAP` | Path | Map CS2 file | *(none)* |
| `CART` | Filename | Cartridge autoload | *(none)* |
| `CART2`, `CART3` | Filename | Additional cartridge banks | *(none)* |
| `USBD` | 1–9 | Override DSKx device to USB | *(none)* |
| `MMEM` | 0 or 1 | MiniMemory enable | 0 |
| `MMMD` | 0 or 1 | MiniMemory mode: 8KB=0, 4KB=1 | 0 |
| `PCODE` | 0 or 1 | Enable PCode card | 0 |
| `MYARC` | 0 or 1 | Enable Myarc 512K | 0 |
| `RAMBO` | 0 or 1 | Enable SAMS at >4000h | 0 |
| `PLAYT` | 0 or 1 | Enable PlayThing ROM | 0 |
| `MHZ` | 250–280 (even) | Pico CPU frequency (MHz) | Build default |
| `NOPS` | 1–9 | Memory read delay cycles | 5 |
| `NOPSW` | 1–9 | Memory write delay cycles | 5 |
| `PSNDO` | 0 or 1 | Enable Digi-Port sound | 0 |
| `FORTI` | 0–3 | ForTI sound mode | 0 |
| `BTJ1` | MAC Addr | Bluetooth Joystick 1 | *(none)* |
| `BTJ2` | MAC Addr | Bluetooth Joystick 2 | *(none)* |
| `BTKM1` | MAC Addr | Bluetooth Keyboard | *(none)* |
| `BTKM2` | MAC Addr | Bluetooth Mouse | *(none)* |
| `BT5KB` | MAC Addr | Bluetooth 5-key Keyboard | *(none)* |
| `BTSPK` | MAC Addr | Bluetooth Speaker | *(none)* |
| `IDE` | 0 or 1 | Enable IDE emulation | 0 |
| `HARDDISK1` | Filename | IDE image 1 | *(none)* |
| `HARDDISK2` | Filename | IDE image 2 | *(none)* |
| `PI.CLOCK` | 0 or 1 | Enable SNTP clock sync | 1 |
| `PI.TCP` | String | Client socket address | *(reserved)* |
| `SOCI` | 0 or 1 | Socket-keyboard input routing | *(reserved)* |
| `RESET` | 0 or 1 | Enable Reset Pin on GPIO8 | *0* |
---

## 📝 Notes:

- Parameters may be freely omitted if not used — defaults will apply.
- File paths are relative to SD root unless using full directory trees.
- Paths are case-sensitive depending on SD card formatting.
- MAC addresses for Bluetooth fields use uppercase `HH:HH:HH:HH:HH:HH` format.

---

# Appendix B — Memory Tester Usage <a name="appendix-b--memory-tester-usage"></a>

---

## 📋 Overview

The PPEB uses external PSRAM modules to enable SAMS and advanced memory features.

Because PSRAM chips can vary significantly in timing tolerance, JasonACT developed a standalone memory test utility to validate stable operation at specific Pico clock speeds.

---

## 💾 Tool Provided

- File: `memtest2.uf2`
- Delivered as: `memtest2.ino.uf2.zip` (extract before use)

This is a standalone firmware image that runs on the Pico W outside of PPEB firmware.

---

## 🖥 Flashing Procedure

1. Disconnect Pico W from PPEB board (or flash prior to full assembly).
2. Connect Pico W to PC via microUSB while holding **BOOTSEL** button.
3. Copy `memtest2.uf2` to the Pico W mass storage device (`RPI-RP2`).
4. Pico will reboot into the memory tester.

> ⚠ This test runs *without* the TI-99/4A attached.

---

## 🧪 Test Behavior

- The Pico W will attempt repeated full PSRAM memory tests.
- If an error occurs, onboard LED will flash blink codes indicating failure.

| LED Blink Pattern | Meaning |
|--------------------|---------|
| Constant solid | Test running, no errors |
| Flashing pattern | Memory failure detected |

- Each failure pattern identifies which test stage failed.

- The test runs continuously to catch intermittent errors caused by marginal chips or thermal changes.

---

## 🔧 Adjusting Pico Timing

If memory errors are detected, you may tune firmware timing via `autoload.cfg`:

```ini
MHZ=258
NOPS=5
NOPSW=5
```

- Lower `MHZ` values slow Pico clock speed for stability.
- `NOPS` and `NOPSW` introduce deliberate read/write delays.
- Use the memory test tool after adjustments to confirm stability before flashing final PPEB firmware.

---

## 📝 Test Recommendations

- Run for several minutes to verify no errors under thermal load.
- Perform test after full PCB assembly to account for solder joint quality.
- Use known-good name-brand PSRAM chips whenever possible.

---

# Credits and Contributors <a name="credits-and-contributors"></a>

---

The PPEB project is from JasonACT, who developed the orignal hardware and software.

This documentation started with a AI scrape from the original AtariAge thread, firmware releases, hardware designs, and builder discussions.

---

## 🧑‍💻 Primary Development Thread

- **Main AtariAge PPEB Development Thread:**  
  [https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/)
  - Warning: This thread has information very distributed throughout, which is why this document was created.
 
---

## 🧑‍💻 Core Firmware Development

- **JasonACT (AtariAge)**  
  - Author and Creator of the original Pi Pico W PEB firmware.  
  - Maintainer of ongoing firmware updates and feature expansions (only software, not hardware)
  - [October 2024 Source code and V2.1 Gerber, V3 Gerber, Dip Trace Files Click Here](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/22/#findComment-5549700)

  - MAME "Speech Synthesizer" stuff... (see GitHub: rgrosset's pico-pwm-audio)

    
- **Latest Firmware Build (Page 28 Post #5639111):**  
  - [Click Here](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/28/#findComment-5639111)
   
---

## 🛠 Hardware Design & Board Work
- **JasonACT**  
  - Original Idea for the project
  - Original Board and schematics
  - Untold Hours of Development
  - V2, and V3 boards.
  - [V2.1 Gerber, V3 Gerber, Dip Trace Files, and October 2024 Source code Click Here](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/22/#findComment-5549700)


- **dabone (Mark Ormond / dabonetn)**  
  - Surface-mount PPEB-cr redesign (PPEB3-SMD v3.2a)  
  - KiCAD PCB layouts & production Gerbers  
  - Hardware builder support only, but knows some of the software.
  - Github repo: [ppeb-cr](https://github.com/dabonetn/ppeb-cr)
    
- **RickyDean, Shift838, dhe, visrealm**  
  - Board assembly, hardware variant testing, firmware validation

---

## 🖨 3D Printable Case Contributors

### V2/V3 Version By JasonACT

[Click Here](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/35/#findComment-5680281)

### PPEBcr (SMD Version)

- **dabone (Mark Ormond / dabonetn)**  

 - Github Link [Click Here](https://github.com/dabonetn/ppeb-cr/tree/main/STLs)

 - **PPZ-00**
  - STL file design for printable enclosures  
  - https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/30/#findComment-5653770
  

---

## 🧪 PSRAM Memory Tester Utility

- **Standalone Memory Test Build (Page 21 Post #5533552):**  
  [https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/21/#comment-5533552](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/21/#comment-5533552)

---

## ✍ Documentation Assembly

- **Acadiel (or Hexbus) (Jon Guidry)**  
  - Full PPEB documentation authoring  
  - GitHub-ready Markdown conversion  
  - Community archival release packaging - it's what I do :)

---

## 📡 Special Thanks

- **AtariAge TI-99/4A Community**  
  - For their deep technical knowledge, real-world testing, and long-term commitment to keeping the TI-99/4A community alive and growing.

---
