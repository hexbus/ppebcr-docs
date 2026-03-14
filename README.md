# PPEB (formal name: Pi Pico W Peripheral Expansion Box Side Port Device)  
*Complete Build & User Manual — TI-99/4A Sideport Expansion* - Last update 10/27/2025

### Wherever possible, we use [reference.txt](https://github.com/dabonetn/ppebcr-docs/blob/main/reference.txt) as the authoritative source, with the [AtariAge thread](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/) as a secondary reference. This is a living, community-maintained doc—PRs and issue reports to fix mistakes are welcome.

> *(Click any section below to jump directly to that part of the manual.)*
# 📑 Table of Contents

- [Project Overview](#project-overview)  
- [Safety Warnings](#safety-warnings)
- [What This Manual Provides](#what-this-manual-provides)
- [Assembly Guides](#assembly-guides)
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

- Board in the size and shape of a real TI Speech Synthesizer
- Speech Synthesizer implementation using PWM
- 32KB Expanded Memory (using Pico memory if desired, but also PSRAM that can implement SAMS, see below...)
- DSR RAM/ROM Memory - 4KB ROM area + 5 * paged 4KB (20KB) RAM areas in >5000=>5FFF
- GRAM/GROM Cartridge + Multi-bank RAM/ROM, E.G. 40KB GROM & 16KB+ ROM (+MiniMem-RAM) +++ PSRAM expands this considerably
- Load   Interrupt Capable (Used to enable the USB subsystem [keyboard & joystick])
- ExtInt Interrupt Capable (Used to enable RS232 non-blocking reads)
- USB Keyboard Interface "<ALT>=" is the normal TI-99 reset, but <CTRL><ALT>DEL can perform a full reset if <ALT>'=' has been locked out,
- USB Keyboard Interface  <CTRL><ALT>F11 is a keyboard to joystick toggle (enables TAB and Arrow keys as a joystick), <CTRL><ALT>F12 is a keyboard-state reset, should keys get stuck
- USB Joystick Interface (4 way Hat switch & 4 buttons + includes 1 analog stick converted to digital)
- USB Mouse interface - TIPI style which can also do Mechatronics style using the available TIPI drivers
- DSK/x - Via the SD Card, including listing directories w/o the 127 file limit
- RAW File read and write support (these work with sectors, but are not direct sector I/O)
- PIO - List to SD Card text-file (/spool.txt)
- Using TCP/IP & standard time-server to set the Pico clock for proper file system timestamps
- SAMS Memory Expansion, using a PSRAM chip, size configurable at build-time
- RS232 - Via Pico's free Serial2 port.  Uses a fixed BAUD rate, see below in the defines, .cfg file can override
- PI.CLOCK - WiFi on the Pico W - Special read-only file (set via a NTP time-server mentioned above) also defined as just 'CLOCK'
- Sockets - WiFi on the Pico W - RS232/2 - port:2322 (Server socket - with single client auto-connect)
- Sockets - WiFi on the Pico W - PI.TCP=... (a single Client-Only socket)
- Sockets - WiFi on the Pico W - Extension-TCP (a single Client-Only socket)
- DSK1 automap when E.G. CALL TIPI("/x/y/DSK1ea/TIEA") loads a module (TI E/A here) from a path with a "last" path component starting with "DSK1" - which gets automapped
- Math Co-Processor implements faster Add (and Sub) Multiply and Divide for Radix-100 floating point numbers (requires USB subsystem)
- PCode Card, with "PCode.ROM", "PCode.GRM" in the SD Card's root, must be 12KB & 62KB (or 64KB) merged G/ROM dumps
- mydisk.DSK sector-dump disk images mapped via CALL MAP1...MAP9 accessed via a special build of the TI Disk Controller Card's DSR ROM (needed for PCode usage, but generally useful)  
  (Note 1: The TI Disk Controller Card's DSR requires sector image file names to end with ".DSK" to be properly detected otherwise SD Card directories are assumed)  
  (Note 2: Mapping a track-dump .DSK file may [or may not] work but it will be converted to a RAM DISK [read only] but can be converted to a sector dump file by the ",RR" postfix)  
- Action-Reply (VDPREGS - because recording them was important to the implementation) <CTRL><ALT>Print-Screen to save, <CTRL><ALT>Win-Menu to restore (Doesn't support SAMS or PCODE)
- Myarc 512KB RAM [switchable] (turns off SAMS to reuse its memory and CRU base - I.E. A Foundation card @ CRU >1E00)
- A USB Memory Stick will mount as a DSK device (DSK3 by default) for a single directory device
- Digi-Port emulation for sound, if the speech synth is enabled
- ForTI sound card emulation using a connected BT speaker
- Bluetooth Gamepads (joystick 1 and 2 specified by the 6 byte BT device address)
- Bluetooth Keyboard & Mouse (ditto^^)
- Console Tester (plus a bootlogger [boot disassembly] via a 2nd separate firmware)
- Reset Switch (via the LOAD int) - Can be configured to enter the PPEB menu from most places
- Debugger Card (Single Step Card)
- IDE Card (USB)
- SPEECH/ALPHON Text to Speech (identical to TEII speech, but available to cartridge Ext Basic[s])
- Sid Master 99 (USB - 2nd Pico/Pico2/W required)
- File Transfer mechanism to back-up/restore/update the SD card in a running TI
- CS3 1200 BAUD cassette interface (uses the CS1 hardware)
- Map Joystick 1 & 2 Buttons (2, 3 and 4 etc.) to Keyboard Keys
- Real Floppy Disk Interface (USB - 2nd Pico2/W required)
- Console GROM emulation (for removing all 3 GROMS from a console) - bad GROM? V2.2 GROM?  You're covered, remove all 3.
- grommy2 interface to flash memory and 4 banks of console GROM + failsafe(s)









  
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
- ✅ TI BASIC Command Reference (`CALL PPEB` / legacy `CALL TIPI`, `CALL EXPON`, etc.)
- ✅ IDE and USB Storage Setup  
- ✅ Advanced Feature Documentation  
- ✅ Troubleshooting Guide  
- ✅ Appendices: Full Config Option Table & Memory Tester Usage



## Hardware 

There are 3 main versions of the PPEB.

The original version with one PSRAM (2MB Sams)
The updated v3 board with dual PSRAM (8MB Sams)
The SMD version with dual PSRAM (8MB Sams), reset button, and rear mounted MicroSD and USB Ports.  

## Assembly Guides <a name="assembly-guides"></a>

Here you will find the guides for the different versions of the PPEB PCBs.

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

---

## 🗃 Firmware Package Contents

[Latest October 4th beta firmware download here.](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/54/#findComment-5726331)

Download the `PPEB2.zip` unzip it, and you should have:

| File / Folder | Purpose |
|----------------|---------|
| `PPEB2_2M.ino.uf2`     | Main firmware binary for 2MB Version (Single PSRAM Chip) |
| `PPEB2_8M.ino.uf2`     | Main firmware binary for 8MB Version (Dual PSRAM Chip) |
| `PPEB2_SID.ino.uf2`    | SID emulation for PPEB (version that needs extra Pico 1) |
| `PPEB2_SID_P2.ino.uf2` | SID emulation for PPEB (version that needs extra Pico 2) | 
| `/ROMS/` | ROM, Bins, and Blank disk files |

(**Note:**  You will use the SID firmware on the stand alone RPI Pico, and the PPEB_2M or PPEB_8M firmware on the actual PPEB.)

---

## 🖥 Flashing Procedure for PPEB firmware 
### (also: steps 1 and 2 will work for the secondary SID Pico and its firmware)

> **⚠ NOTE:** The Pico W must be flashed BEFORE inserting it into the TI-99/4A system!

### 1️⃣ Connect Pico W

- Do not have a MicroSD card inserted.
- Do not have anything plugged into the rear USB port.
- Hold down the **BOOTSEL** button on your Pico W.
- While holding the button, connect the Pico to your PC using a MicroUSB cable.
- The Pico should appear as a USB mass storage device named **RPI-RP2**.

### 2️⃣ Flash UF2 Firmware

- Pick the Correct Firmware for your device - If you have 1 PSRAM, then select the 2MB version, 2 PSRAMs, select the 8MB version.
- Copy the file `PPEB.uf2` directly onto the Pico’s USB drive.  These files are only [located on AtariAge](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/28/#findComment-5639111).
- After the file copies, the Pico will automatically reboot into PPEB mode.

**Startup LED blink check**

- **3 blinks** → No MicroSD card detected, or the SD card is missing required files. (Expected with no card.)
- **1 blink** → Common observed behavior when a MicroSD card with the correct files is present. (Some builds may differ slightly.)

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

The easiest way to do this is just copy the content of the roms directory to the root of your SD card.

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

> P-CODE files must be in the SD card root.  
> `PCode.ROM` = 12 KB, `PCode.GRM` = 62–64 KB.


 A sample card image can be found here. [Download Sample Card Image](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/28/#findComment-5616188).

Just unzip it to the root of your SD card. If you have done this correctly you will have the following also appear in the root of the SD card.



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
D1MAP=/BLNK.DSK
CART=/PICOPEB
RESET=1
```

> A full list of all supported configuration fields will be documented in the [Configuration Reference](#configuration-file-reference-autoloadcfg).

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

Place **PICOPEBG.BIN** on the **root** of the SD card, then add this to `autoload.cfg`:

```ini
CART=/PICOPEB
```
If you have the reset button installed or a PPEB-cr version, then also add RESET=1 to allow returning to the PPEB menu with the reset button.
(I think this is incompatible with a rs232 adapter installed,
<br> I've not tested the rs232 function)

> Note: On **single-PSRAM (2 MB)** builds, using the physical 3.3 V UART conflicts with the RESET-on-GPIO8 option. If you wire the UART, remove `RESET=1`.

---

### 📂 File Format

- File name: `autoload.cfg`
- Format: Plain text (ASCII)
- One setting per line: `KEY=VALUE`
- No quotes needed around values
- Blank lines and comment lines (starting with `;`) are allowed
- Maximum of 2048 Characters are read, the rest are ignored.
Example:

```ini
CRU=1
WIFI=MyWiFi
PASS=SuperSecret
SNTP=pool.ntp.org
```

---

## 🖧 System Configuration Example Settings
(Note: Full file settings are available here:  https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/55/#findComment-5736055)

| Parameter | Description | Example |
|-----------|-------------|---------|
| `CRU` | CRU base offset for DSR (TI-99 sideport address base) | `CRU=1` (→1100h) |
| `RESET` | Enable Reset Button 1 for Default Cart / 2 for Snapshot and Enter PPEB Menu | `RESET=1` |
| `BAUD` | RS232 baud rate override | `BAUD=9600` |
| `MODE` | RS232 mode, Default 8,N,1 | `MODE=1043` |
| `WFCC` | WiFi Country Code | `WFCC=XX` |
| `WIFI` | WiFi SSID name | `WIFI=YourSSID` |
| `PASS` | WiFi password | `PASS=YourPassword` |
| `SNTP` | Time server (NTP hostname) | `SNTP=pool.ntp.org` |
| `TZHR` | Timezone offset (hours) | `TZHR=-5` |
| `TZMN` | Timezone offset (minutes) | `TZMN=0` |
| `TMFT` | Date format: `0`=US, `1`=AU | `TMFT=0` |
| `NOSYNTH` | Turns Off Speech Synth Emulation | `NOSYNTH=1` |
| `SPEECH`  | Turns on the SPEECH device [Terminal Emulator II] for the text to speech DSR functions SPEECH and ALPHON| `SPEECH=1`|
| `FYPD` | Enable Special Pico driven USB Floppy Drive (See next item) | `FPYD=1` |   
| `USBD` |  If a USB memory stick is inserted, this DSKx number will be temporarily overridden,value from 1 to 9 | `USBD=2` |
| `CART`| CART=, CART2=, CART3=, etc. to autoload command modules | 'CART=PICOPEB` |
| `MENUS`| Turns on the cartridge menu lookup for all carts, not just PHM filenames | `MENUS=1`|
| `TSTR`| Turns on the Console Tester, file "constest.rom" is provided | `TSTR=1` |
| `KBLOAD`| Turns on [CTRL][ALT][PAUSE] as the LOAD interrupt button | `KBLOAD=1` |
| `TIMEST`| Turns on 146 byte length directory records, default is 0 which is the standard 38 byte length records, =2 makes timestamps 2 digit years | `TIMEST=1`|
| `SID`| Turns on the Sid Master 99 device | `SID=1`
| `XFER`| The machine:port to connect to in the AID menu to transfer files to/from the SD card. | `XFER=192.168.0.100:1000` |
| `MARGIN` | Shift the PPEB menu selection to the right for badly configured screens, Can be 1 or 2 | `MARGIN=1`|
| `GROMS` |Sets the "EEPROM location 2-5" to 1="GROM", 2="GR22", 3..="GRM0".."GRM4" (4=failsafe) which enables Console GROM emulation on the next boot | 'GROMS=4'|


## Serial Mode Settings: (MODE=)

PARITY_EVEN   0x001  
PARITY_ODD    0x002  
PARITY_NONE   0x003  
PARITY_MARK   0x004  
PARITY_SPACE  0x005  

STOP_BIT_1    0x010  
STOP_BIT_1_5  0x020  
STOP_BIT_2    0x030  

DATA_5        0x100  
DATA_6        0x200  
DATA_7        0x300  
DATA_8        0x400  

I.E.  
8N1 = 0x413, converted to decimal MODE=1043  
8E1 = 0x411, converted to decimal MODE=1041  

# WiFi Country Codes  
Do not change this frequently, it will wear out the flash memory page, also requires a power-cycle when first setting up or changing.

| Country | Code |Country | Code |Country | Code |Country | Code |Country | Code |
|----------------|--|----------------|--|----------------|--|----------------|--|----------------|--|
|`WORLDWIDE`      |`XX`|`Default`
|`AUSTRALIA`      |`AU`|`ESTONIA`   |`EE`|`ITALY`        |`IT`|`NETHERLANDS`|`NL`|`SLOVENIA`    |`SI`|
|`AUSTRIA`        |`AT`|`FINLAND`   |`FI`|`JAPAN`        |`JP`|`NEW_ZEALAND`|`NZ`|`SOUTH_AFRICA`|`ZA`|
|`BELGIUM`        |`BE`|`FRANCE`    |`FR`|`KENYA`        |`KE`|`NIGERIA`    |`NG`|`SOUTH_KOREA` |`KR`|
|`BRAZIL`         |`BR`|`GERMANY`   |`DE`|`LATVIA`       |`LV`|`NORWAY`     |`NO`|`SPAIN`       |`ES`|
|`CANADA`         |`CA`|`GREECE`    |`GR`|`LIECHTENSTEIN`|`LI`|`PERU`       |`PE`|`SWEDEN`      |`SE`|
|`CHILE`          |`CL`|`HONG_KONG` |`HK`|`LITHUANIA`    |`LT`|`PHILIPPINES`|`PH`|`SWITZERLAND` |`CH`|
|`CHINA`          |`CN`|`HUNGARY`   |`HU`|`LUXEMBOURG`   |`LU`|`POLAND`     |`PL`|`TAIWAN`      |`TW`|
|`COLOMBIA`       |`CO`|`ICELAND`   |`IS`|`MALAYSIA`     |`MY`|`PORTUGAL`   |`PT`|`THAILAND`    |`TH`|
|`CZECH_REPUBLIC` |`CZ`|`INDIA`     |`IN`|`MALTA`        |`MT`|`SINGAPORE`  |`SG`|`TURKEY`      |`TR`|
|`DENMARK`        |`DK`|`ISRAEL`    |`IL`|`MEXICO`       |`MX`|`SLOVAKIA`   |`SK`|`UK`          |`GB`|
|`USA`        |`US`|||||||||

(When changing the .cfg file to a new value, power-up the TI and wait for the title screen, then power-down and power-up again.)
---


## 🔧 Grommy 2 Emulation

This option can replace the original GROM chips inside the ti99/4a itself.  

To use the new GROMMY2 modes, you will need to program banks 0-3 yourself.  
Use the GROMS=1, GROMS=2 (V2.2) or GROMS=7 (Gary's modified for a 9938) to do the inital programming.

PPEB GROMMY2 modes:


| Parameter | Description |
|-----------|-------------|
|GROMS=0 |Off
|GROMS=1 |Original
|GROMS=2 |V2.2
|GROMS=3 |GROMMY2 - bank 0
|GROMS=4 |GROMMY2 - bank 1
|GROMS=5 |GROMMY2 - bank 2
|GROMS=6 |GROMMY2 - bank 3
|GROMS=7 |GROMMY2 - failsafe

--- 



## 💾 File System & Drive Mapping

| Parameter | Description | Example |
|-----------|-------------|---------|
| `D1MAP` → `D9MAP` | Map DSK folders or images | `D1MAP=/BLNK.DSK` |
| `C1MAP` | Map CS1 file | `C1MAP=/CS1/file` |
| `C2MAP` | Map CS2 file | `C2MAP=/CS2/file` |
| `CART` | Cartridge image to autoload | `CART=MyCart` |
| `CART2` (CART3 not used) | Additional multi-bank cartridges | `CART2=AnotherCart` |
| `USBD` | If a USB stick is present, map **that** device as `DSKx` (choose 1–9) | `USBD=1` (maps USB to DSK1) |


- CART note: `CART2` adds a Review Module Library entry on the TI title screen; `CART3` is not implemented due to memory constraints. (thread page 47)

---

## 🧮 Memory & Emulation Settings

| Parameter | Description | Example |
|-----------|-------------|---------|
| `MMEM` | Enable Mini-Memory RAM | `MMEM=1` |
| `MMMD` | Mini-Memory mode: `0`=8KB, `1`=4KB | `MMMD=0` |
| `PCODE` | Enable PCode Card emulation | `PCODE=1` |
| `MYARC` | Enable Myarc RAM emulation (512K) | `MYARC=1` |
| `RAMBO` | Enable SAMS RAM at >4000 when DSR ROM disabled | `RAMBO=1` |
| `PLAYT` | PlayThing ROM/RAM enable (DSR Testing Only) | `PLAYT=1` |
| `PLAYT` | PlayThing ROM/RAM enable (Serial) | `PLAYT=2` |
| `PLAYT` | PlayThing ROM/RAM enable (Wifi) | `PLAYT=3` |
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
| `PSNDO` | Enable Digi-Port 8-bit audio via Pico PWM | `PSNDO=1` |
| `FORTI` | ForTI sound card emulation via Bluetooth (`0`=off, `1-3` modes) | `FORTI=1` | 
| `FORTI=1` | one chip emulated    - plays left & right channel in mono the same as the TI | `FORTI=1` | 
| `FORTI=2` | 4 chips emulated - plays in stereo, | `FORTI=2` | 
| `FORTI=3` | 4 chips emulated - plays in mono | `FORTI=3` | 

Forti Sound is ONLY available using a Bluetooth Speaker, it is not mixed into the onboard sound of the TI.

---
## 🕹 Joystick Button Remapping.

You can remap joystick buttons (usb/bt) to keyboard keys.
The format is  

JOY12= through to "19=" & lastly "10=" <- joystick 1 button 2 to 10  

JOY22= through to "29=" & lastly "20=" <- joystick 2 button 2 to 10  

 =ENTER / =SPACE (whole word) or any letter / number / symbol (single character - being the main one listed on the key:  '=' '/' ';' ',' '.').  
 You can't use the USB/BT keyboard at the same time as the joysticks when this is enabled in the config (it will get confused). 

| Parameter | Description | Example |
|-----------|-------------|---------|
| `JOY12` | Map Joystick 1 Button two to A | `JOY12=A` |
| `JOY14` | Map Joystick 1 Button four to Space | `JOY12=SPACE` |
| `JOY22` | Map Joystick 2 Button two to A | `JOY22=A` |
| `JOY24` | Map Joystick 4 Button four to Enter | `JOY24=ENTER` |---

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
| `IDE` | Disable IDE emulation | `IDE=-1 or Remove IDE from the Autoload.cfg` |
| `IDE` | Enable IDE emulation (Use 0 thru D to select CRU Address) | `IDE=1` |
| `HARDDISK1` | First IDE image file | `HARDDISK1=harddisk1.dat` |
| `HARDDISK2` | Second IDE image file | `HARDDISK2=harddisk2.dat` |

- Image files must be placed on a connected USB drive (not SD card).

---

## 🖧 TIPI Extension & TCP/IP Options

| Parameter | Description | Example |
|-----------|-------------|---------|
| `PI.CLOCK` | Enables SNTP time setting (**note:** earlier builds defaulted to 0) | `PI.CLOCK=1` |
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

The PPEB adds numerous device service routines (DSRs) and control commands accessible directly from TI BASIC, Extended BASIC, and assembly programs.

This section documents all supported `CALL` commands.

> **Note on command name:** On newer builds, `CALL PPEB` replaces `CALL TIPI`.  
> Many setups autostart the PPEB menu at boot, so you may not need to type either command.

---

## 🖥 Storage & TIPI Commands

| Command | Description |
|---------|-------------|
| `CALL PPEB` (or legacy `CALL TIPI`) | Launch the PPEB/TIPI-style menu browser — allows loading cartridges, modules, and configurations. |
| `CALL TIPI' (or legacy `CALL TIPI`) ("/cartprefix")  | Loads GROM/ROM with a cartridge SD card image (C/D/8/-8/_8/_9/G), MAME .ZIP/.RPK (don't type .ext), or EA5 loader (TIFILES & no .ext). |
| `CALL UNMOUNT` | Cleanly unmount SD card before removal (Also saves the bluetooth & IDE config, remounts automatically on next access). |

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
| `CALL FILES(n)` | List files for DSKn. **Requires CRU base >1100h**. |
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


  ## [Click Here For How to Install the IDEDSR to the PPEB](/idedsr.md)
  
---

## 🖧 Hardware Notes

- IDE images must be stored on a USB memory stick attached to the PPEB.
- The Pico W enumerates the USB drive automatically on startup.
- You may use a powered USB hub to allow multiple USB devices simultaneously (keyboard, mouse, joystick, USB drive).

---

## 📂 File Structure on USB

Format the USB drive **exFAT** (recommended). RAW devices are also supported.




```bash
/
├── harddisk1.dat      <-- IDE drive 1 image
├── harddisk2.dat      <-- IDE drive 2 image (optional)
```

- Subdirectories are not recommended.
- Image files are raw sector dumps, not filesystem dumps.
- File sizes up to ~2GB per image supported.   Large images have been tested; for reliability keep each image ≤ ~2 GB.
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

## Minimal `autoload.cfg` for IDE testing

```ini
CART=/PICOPEB
IDE=1
HARDDISK1=harddisk1.dat
```

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
| TI99 Hanging on Boot|USB Formatted as FAT32|Format USB as exFAT
| IDE not detected | USB drive not seen | Try different hub port |
| Hard disk not formatted | Blank image used | Use Myarc utilities to initialize |
| Image too large | Unsupported file size | Keep images ≤ 2GB |

- Some USB hubs enumerate devices in varying orders — try swapping ports if devices conflict.
- Always eject USB drive safely from PC before inserting into PPEB.

---

## 📝 Notes

- The IDE interface works fully in conjunction with TIPI, SAMS, Myarc RAM, and DSR functions.
- Only one USB memory stick may be used for IDE storage per session.
- You don't need to unmount the USB stick, just don't pull it out while you're using it.

---

# Advanced Features <a name="advanced-features"></a>

---

## 📋 Overview

The PPEB replicates — and in many cases expands — multiple classic TI expansion subsystems inside one sideport device.

This section summarizes each advanced subsystem available once fully configured.

---

## 🔧 SAMS Memory Expansion

- Fully supports 2MB or 8MB SAMS configurations.
  - On dual-PSRAM boards, SAMS paging up to **8 MB** is supported and has been exercised in demos/utilities; single-PSRAM boards support **2 MB**.
Notes:
  - Most classic software was written for ≤1 MB, but newer builds/demos use >1 MB.
  - PPEB firmware and tooling have examples showing 8 MB setups working on dual-PSRAM boards.
   
- Uses external PSRAM modules connected to Pico W.
- Activated automatically when present.
- SAMS memory functions as expanded RAM for compatible software.
- Works with FinalGROM99, RXB, UCSD PCode, etc.

---

## 🔧 Myarc RAM Emulation (512KB)

- Emulates Myarc 512KB RAM card for compatible software.
- Swaps SAMS space dynamically to operate within Myarc RAM specifications.

*Note: Enabling Myarc 512 KB mode disables SAMS since they share the CRU base/memory space.*

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
PLAYT=0 (Off)
PLAYT=1 (On, but with no interface to Serial/WiFi - just a 4KB RAM DSR for testing stuff)
PLAYT=2 (On, with an interface to Serial - set MODE and BAUD for this to work)
PLAYT=3 (On, with an interface to WiFi - no need to set MODE and BAUD)
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

> **P-CODE files must reside in the SD card root.**  
> `PCode.ROM` is **12 KB**; `PCode.GRM` is **62–64 KB** depending on dump.

---

## 🔧 TIPI Extensions

- Partial TIPI extensions are supported:
  - File system access via `CALL TIPI`.
  - Network time synchronization (`SNTP`, `PI.CLOCK`).
  - Partial mouse extensions.
  - TCP/IP socket server (`RS232/2`) and client.

> ⚠ Not a full TIPI replacement — designed as an integration layer.


## 🔧 TI DSR Devices Available

TI DSR Devices available:  

| DSR | Description|
|---------|-------|
|TIPI| SD card root access  |
|DSK|  SD card root access (allows access to disks via their name, if a directory in SD Root is that name, or a .DSK is mounted)  |
|DSK1| /DSK1/ SD card access + AUTOMAP (all current maps survive a reset)  |
|...|  |
|DSK9| /DSK9/ SD card access  |
|PIO|  spool.txt SD card access|  
|PIO/1|spool.txt SD card access (same as above)  |
|RS232| Pico serial port access  |
|RS232/1| Pico serial port access (same as above)  |
|RS232/2| Pico tcpip port:2322 access  |
|PI| Currently, only PI.CLOCK as a read-only file & PI.TCP as a single client socket are implemented  |
|CLOCK| Same as PI.CLOCK  |
|SOCI|  RS232/2's socket for output, but socket input is directed into the special keyboard CRU input interface |
 
---
  

## 🔧 RS232 Serial Interfaces: physical 3.3 V UART (single-PSRAM/2 MB build only) or TCP/IP socket (RS232/2) over Wi-Fi.

> **8 MB (dual-PSRAM) builds do not expose the physical 3.3 V UART; all RS232 devices map to the Wi-Fi socket server.**

### Option 1 — Physical RS232 (3.3V - not on 8MB boards)

- Available via Pico's UART2 interface:
  - `GP8 → TX`
  - `GP9 → RX`

- Logic-level (3.3V) only — use external level shifter for RS232 compliance.

- Baud rate set via:

```ini
BAUD=9600
```

### Option 2 — TCP/IP Socket RS232

- Virtual serial over Wi-Fi on **TCP port 2322**
- Accessed on the TI as: `RS232/2`
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

- Matches TI Speech DSR functionality for software compatibility.

- Audio routing: PWM speech is **mixed into the TI console sound input** via the 44-pin sidecar connector, so you hear it on your normal monitor/speakers.
    
---

## 🔧 Digi-Port 8-bit Audio

- Provides additional PCM-style audio output.

- Controlled via:

```ini
PSNDO=1
```

- Output routing: Digi-Port PCM is generated by Pico PWM and **mixed into the console sound input** on the 44-pin sidecar connector.

---

## 🔧 ForTI Sound Card Emulation

- Emulates multi-chip TI ForTI sound expansion:

- ForTI output is sent to a Bluetooth speaker, and needs Bluetooth connected for you to be able to use, and you might hear a slight delay in the output

```ini
FORTI=1  (or 2 or 3)
```

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
- Use the CALL UNMOUNT command in basic to save the Bluetooth config.
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
| Frequent freezes when loading ROMs | PSRAM instability | Use `memtest2.uf2` (See Appendix B) to verify PSRAM stability. Adjust `MHZ`, `NOPS`, and `NOPSW` in `autoload.cfg`. |
| Board not powering on | Faulty sideport edge connector or bad solder joints | Inspect all PCB solder joints, verify Pico power pins. |

---

## 💽 SD Card Problems

| Symptom | Cause | Solution |
|---------|-------|----------|
| PPEB not detected at all | Missing SD card or unreadable card | Verify card inserted; reformat as FAT32 (MBR). |
| Autoload failing | Corrupt or missing `autoload.cfg` | Verify syntax, file format (plain text). |
| File not found errors | Incorrect directory structure | Verify SD card file/folder layout matches documentation. |

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
| Image won't load | Image file too large |  Large images have been tested; for reliability keep each image ≤ ~2 GB. |

---

## 🎮 USB & Bluetooth Device Issues

| Symptom | Cause | Solution |
|---------|-------|----------|
| USB devices not detected | Hub order issue | Use powered hub; verify enumeration order. |
| Bluetooth device won't pair | Incorrect MAC address | Pair on a PC to get the MAC → put MAC in autoload.cfg (BTJ1, etc.) → put device in pair mode → power TI → CALL UNMOUNT to save. |
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
| Supports | **Not required (self-supporting design)** |
| Material | PLA, PETG, or ASA |
| Print Orientation | Flat on bed, face-down recommended |
| Wall Count | 3+ walls for durability |

---

## 🔩 Assembling Hardware

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
| `CRU` | Integer (Hex Digit 0–D) | CRU base >1100h | 1 |
| `BAUD` | Integer | RS232 baud rate | 9600 |
| `WIFI` | String | WiFi SSID | *(none)* |
| `PASS` | String | WiFi password | *(none)* |
| `SNTP` | String | NTP time server | *(none)* |
| `TZHR` | Integer | Timezone offset (hours) | 0 |
| `TZMN` | Integer | Timezone offset (minutes) | 0 |
| `TMFT` | 0 or 1 | Date format: 0=US, 1=AU | 0 |
| `D1MAP`–`D9MAP` | Path | Map DSK1–DSK9 to folders or .DSK images | *(none)* |
| `C1MAP` | Path | Map CS1 file | *(none)* |
| `C2MAP` | Path | Map CS2 file | *(none)* |
| `CART` | Filename | Cartridge autoload | *(none)* |
| `CART2` (CART3 not used) | Filename | Additional cartridge banks | *(none)* |
| `USBD` | 1–9 | Override DSKx device to USB | *(none)* |
| `MMEM` | 0 or 1 | MiniMemory enable | 0 |
| `MMMD` | 0 or 1 | MiniMemory mode: 8KB=0, 4KB=1 | 0 |
| `PCODE` | 0 or 1 | Enable PCode card | 0 |
| `MYARC` | 0 or 1 | Enable Myarc 512K | 0 |
| `RAMBO` | 0 or 1 | Enable SAMS at >4000h | 0 |
| `PLAYT` | 0–3 | Enable PlayThing ROM | 0 |
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
| `IDE` | -1 or (Use 0 thru D to select CRU Address)  | Enable IDE emulation | -1 |
| `HARDDISK1` | Filename | IDE image 1 | *(none)* |
| `HARDDISK2` | Filename | IDE image 2 | *(none)* |
| `PI.CLOCK` | 0 or 1 | Enable SNTP clock sync | 1 |
| `PI.TCP` | String | Client socket address | *(reserved)* |
| `SOCI` | 0 or 1 | Socket-keyboard input routing | *(reserved)* |
| `RESET` | 0 or 1 | Enable Reset Pin on GPIO8 | 0 |
---

## 📝 Notes:

- Parameters may be freely omitted if not used — defaults will apply.
- File paths are relative to SD root unless using full directory trees.
- MAC addresses for Bluetooth fields use uppercase `HH:HH:HH:HH:HH:HH` format.
- CART2 adds a Review Module Library entry on the TI title screen; CART3 is not implemented due to memory constraints (thread page 47)
---

# Appendix B — Memory Tester Usage <a name="appendix-b--memory-tester-usage"></a>

---

## 📋 Overview

The PPEB uses external PSRAM modules to enable SAMS and advanced memory features.

Because PSRAM chips can vary significantly in timing tolerance, JasonACT developed a standalone memory test utility to validate stable operation at specific Pico clock speeds.

---

## 💾 Tool Provided

- File: `memtest2.uf2` - Locate the latest memory test on [this thread](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/) on AtariAge
- Delivered as: `*.memtest2.ino.uf2.zip` (extract before use)

This is a standalone firmware image that runs on the Pico W outside of PPEB firmware.

---

## 🖥 Flashing Procedure

1. Disconnect Pico W from PPEB board (or flash prior to full assembly).
2. Connect Pico W to PC via MicroUSB while holding **BOOTSEL** button.
3. Copy the latest version of `memtest2.uf2` as linked above to the Pico W mass storage device (`RPI-RP2`).
4. Pico will reboot into the memory tester.

> ⚠ This test runs *without* the TI-99/4A attached.

---

## 🧪 Memory Test Tool Behavior

- The Pico W will attempt repeated full PSRAM memory tests.
- If an error occurs, onboard LED will flash blink codes indicating failure.

**Startup LED blink check (normal behavior):**

| LED Blink Pattern | Meaning |
|--------------------|---------|
| Constant solid | Test running, no errors |
| Flashing pattern | Memory failure detected |

- Each failure pattern identifies which test stage failed.

- The test runs continuously to catch intermittent errors caused by marginal chips or thermal changes.

### 🔧 Adjusting Pico Timing if errors detected

If memory errors are detected, you may tune firmware timing via `autoload.cfg`:

```ini
MHZ=258
NOPS=5
NOPSW=5
```

- Lower `MHZ` values slow Pico clock speed for stability.
- `NOPS` and `NOPSW` introduce deliberate read/write delays.
---

### 📝 Test Recommendations

- Run for several minutes to verify no errors under thermal load.
- Perform test after full PCB assembly to account for solder joint quality.
- Use known-good name-brand PSRAM chips whenever possible.

---

### Return the unit back to regular firmware when done

- Ensure you flash the regular PPEB firmware back to the unit after completing the memory test.

---

# Credits and Contributors <a name="credits-and-contributors"></a>

---

The PPEB project is from JasonACT, who developed the original hardware and software.

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
  - Most current firmware will always be linked under the [firmware installation](#firmware-installation) section

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

## 📜 Licensing & Source

- PPEB firmware sources are available in the AtariAge thread.  
- The most recent full archive released by JasonACT is **PPEB2_Src.zip (October 16, 2024)**, attached here:  
  [AtariAge thread, Oct 16, 2024](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/?do=findComment&comment=5549947).  
- Jason’s stated license for the combined firmware is **CC BY-NC-ND 4.0**.  The last release was on October 16, 2024 (linked above), and Jason desires to keep his releases on AtariAge on the linked thread to meet said compliance.
- The Resid16 (sidkic-pico) source was released seperately inside the thread, as it lives on a seperate Pico board.
- For personal builds compiled from source, no additional obligations apply.  

The PPEB firmware and hardware integrate components from multiple upstream projects. Each retains its own license terms.

| Component                | License                              | Upstream Repo                                                | Requirements                                                                                                                 |
|--------------------------|--------------------------------------|-------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| **Resid16 (sidkick-pico)** [note: seperate from PPEB - on a different board, so different project] | GPLv2 (software)<br>+ CC BY-NC-ND 4.0 (hardware) | https://github.com/frntc/SIDKick-pico                       | If a binary (UF2) contains this code, you must provide the complete corresponding source for that binary, including any glue code. If it’s run as a separate firmware on a separate Pico, only that firmware’s source needs to be released. The CC BY-NC-ND hardware license applies only if you use or distribute their PCB design — not if run on a plain Raspberry Pi Pico.  (Note: Jason released the source in the thread for the Pico code, which runs on its own Pico.) |
| **Pico-SDK**             | BSD 3-Clause                         | https://github.com/raspberrypi/pico-sdk                     | Include license text and attribution in distribution. No source release required.                                           |
| **lowzip**               | MIT                                  | https://github.com/edrosten/lowzip                          | Include license text and attribution. No source release required.                                                            |
| **MAME speech (tms5110.cpp)** | BSD 3-Clause                    | https://github.com/mamedev/mame/blob/master/src/devices/sound/tms5110.cpp | Include license text and attribution. No source release required. |
| **opt5.a99 (Jedimatt)**  | Unlicense (public domain)            | https://github.com/jedimatt42/ti994a-opt5                   | No requirements.                                                                                                             |
| **Keyboard code**        | None (structural code only, attribution sufficient)  | https://github.com/jedimatt42/TI-99-usb-keys/blob/main/LICENSE | Attribution only.  Verified no license needed due to only using structure.  See https://github.com/felis/USB_Host_Shield_2.0/issues/846 |
| **Tursi (Mike Brent / HarmlessLion)** | "Please tell me when you use my code" | http://harmlesslion.com                                     | JasonACT requested and received permission from Tursi to use portions of his TI code in PPEB firmware (confirmed in AtariAge [thread](https://forums.atariage.com/topic/358129-pi-picow-peripheral-expansion-box-side-port-device/page/42/#findComment-5700703). |


---

## 📡 Special Thanks

- **AtariAge TI-99/4A Community**  
  - For their deep technical knowledge, real-world testing, and long-term commitment to keeping the TI-99/4A community alive and growing.

---
