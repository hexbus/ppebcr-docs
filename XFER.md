# XFER — WiFi File Transfer (PC ↔ TI, while running)

XFER transfers files between a PC and the PPEB's SD card **over WiFi, while the TI is powered on** — no card swapping. It shipped in the 2025-12-16 firmware release and is easy to miss: the config reference lists the `XFER=` parameter, but the workflow and the PC-side tool live in forum posts. This page pulls it together.

## Requirements

- PPEB firmware **2025-12-16 or later** (earlier builds silently ignore the trigger key — if pressing `T` on the AID screen does nothing, your firmware predates XFER)
- The PPEB connected to your WiFi (`WIFI=` / `PASS=` in `autoload.cfg`)
- The PC-side tool `PPEB2_XFER` (`.exe` for Windows, plus `PPEB2_XFER.c` source that compiles on Linux/macOS/RPi), included in the `Software/` (formerly `ROMS/`) folder of firmware releases

## Setup

1. Add to `autoload.cfg` on the SD card:

   ```
   XFER=192.168.1.50:10000
   ```

   where `192.168.1.50` is **your PC's IP address**. The port must be **10000** when using the stock `PPEB2_XFER` tool (it is hardcoded there).

2. Remember config changes are read on **cold boot** (power up → title → power off → power on).

3. First run on Windows: allow `PPEB2_XFER.exe` through the firewall for your network profile (it listens for an incoming connection). If the transfer times out, check the firewall allowed the *correct* profile (Private vs Public — see `Get-NetConnectionProfile` in PowerShell).

## How a transfer works

The direction of connection surprises people: **the PC tool is the server** — it starts, listens, and waits. **The PPEB is the client** — it connects out to the PC when you trigger it from the TI. Every transfer is therefore two steps:

1. **On the PC** — run the tool with what you want done (it prints `Starting` and waits):

   ```
   PPEB2_XFER S /subdir/ files*.rpk     Send file(s)/wildcards from the PC to the SD card
   PPEB2_XFER R /subdir/file.ext dir\   Retrieve a file from the SD card to the PC
   PPEB2_XFER R /subdir/ dir\           Retrieve ALL files in an SD directory
   PPEB2_XFER D /subdir/                Display an SD directory listing
   ```

   Send destinations must start *and* end with `/` (use `/` for the SD root). On Linux, wildcard support is limited to a single `*`.

2. **On the TI** — `CALL PPEB` → press **FCTN+7** (AID screen) → press **T**. The PPEB connects to the PC, the transfer runs, and the TI reports *transfer complete*.

   Alternative trigger: **CTRL+ALT+INSERT** on a USB/BT keyboard attached to the PPEB initiates the connection at any time, without entering the AID screen — but be careful the TI isn't mid-operation.

## Wire protocol

For anyone writing their own PC-side tool (it's ~50 lines in any language) — decoded from `PPEB2_XFER.c`:

- TCP, PPEB connects to the host:port from `XFER=`. One connection per trigger.
- **Sending a file to the SD card**, per file:

  ```
  [1 byte]  destination path length
  [n bytes] destination path, e.g. /GAMES/FOO.RPK
  [4 bytes] payload length, big-endian
  [n bytes] payload
  ```

  Repeat for each file; end the session with a single `0x00` byte (zero-length filename).
- **Requesting data from the SD card** (file fetch or directory listing):

  ```
  [0xFF] [1 byte name length] [name]
  ```

  then receive `[4 bytes big-endian length][payload]`. If the name ends in `/`, the payload is a directory listing: one entry per line (control-character terminated), files prefixed `F `.

## Notes

- Files land on the SD card live; a pushed cartridge (`.RPK`/`.ZIP` per the `CALL TIPI` conventions) is immediately loadable from the PPEB menu.
- RPK reminder: the PPEB's cartridge loader matches the **filenames inside the archive** (must end `C`/`D`/`G`/`8`/`9` + `.BIN`), not `layout.xml`.
- The `T` trigger is operator-initiated by design; there is no fully PC-initiated transfer today. (The CTRL+ALT+INSERT path via a USB HID device is a possible automation route.)
