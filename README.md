# LG KF300 — UART flash guide (GSM-Multi / Hermes)

Historical notes for recovering an **LG KF300** (ADI SoftFone, Hermes / AD6527) when USB BootROM is dead and the phone soft-bricks on gallery/folders (FS/NAND corruption).

**This repository contains documentation only — no installers, DLLs, or firmware binaries.**  
Download the verified pack from the Internet Archive (links below), check SHA256, and scan the `.exe` on VirusTotal.

Verified working flash: **2026-09-27** (GSM-Multi V3.0, UART CH340, Pass).

---

## Links

### Flash pack (binaries)

| Resource | URL |
|----------|-----|
| **Flash pack (ZIP)** | https://archive.org/details/kf-300-flash-windows |
| Related IA item (Multi / DLL fragments) | https://archive.org/details/lg-dll-files |
| Multi installer on IA (inside RAR) | https://archive.org/download/lg-dll-files/%24RCT1LM6.rar |
| KF300 DLL on IA | https://archive.org/download/lg-dll-files/KF300_080313.dll |

Pack expected contents: `SETUP_GSMULTI_V30.exe` + `KF300_080313.dll` + `KF300AT-00-V10l-CIS-XXX-APR-17-2008.bin`  
Hashes: see [SHA256.txt](./SHA256.txt)

### Guide in this repo

| Doc | What |
|-----|------|
| [KF300_FLASH_GUIDE.md](./KF300_FLASH_GUIDE.md) | Full procedure (working order, pinout, anti-patterns) |

### Community / historical threads

| Resource | URL |
|----------|-----|
| Unlockers — KF300 flash thread (archive) | https://www.unlockers.ru/archive/index.php/t-19971.html |
| Unlockers — same thread (live) | https://www.unlockers.ru/threads/19971-LG-KF300-%D0%BF%D0%BE%D0%BC%D0%BE%D0%B3%D0%B8%D1%82%D0%B5-%D0%BF%D1%80%D0%BE%D1%88%D0%B8%D1%82%D1%8C |
| Chinese UART + MultiGSM text guide (KF300) | http://shouji.pc004.com/xuangou/2009/09/08/2185552.shtml |
| GSM-Forum (related KF300 cable/tools discussion) | https://gsmforum.ru/threads/kf300-ot-lg-kabel-programmy-draivery.81503/ |

### Drivers / tools (external)

| Resource | URL |
|----------|-----|
| WCH CH340/CH341 Windows driver | http://www.wch-ic.com/downloads/CH341SER_EXE.html |
| VirusTotal | https://www.virustotal.com/ |

---

## Quick facts (do not improvise)

- Port: **UART**, baud **921600**, ADI boot **Hermes (AD6527)**  
- Start Com = End Com = **one** CH340 COM (do not scan 1–16)  
- Board (CN300 ripped): CH340 **RXD→R310** (phone TX), **TXD→R311** (phone RX), GND  
- **5V → 47 kΩ → R101 (EXT_PWRON)** is a **pulse after** Multi is Waiting and the battery is inserted — not permanently from the start  
- CH340 logic jumper **3.3V**; do **not** wire module 3.3V/VCC to the phone  
- Remove Type-C / VBUS during flash (USB_DET breaks the UART path)  
- Never pull battery/USB while percentages are running  

Details: [KF300_FLASH_GUIDE.md](./KF300_FLASH_GUIDE.md)

---

## License / rights

Firmware and GSM-Multi are third-party / discontinued mobile service tools (© respective owners, rights status unknown).  
This repo ships **text documentation only**, for archival and repair of a long-obsolete device.

---

## Changelog

- 2026-09-27 — docs published after successful Pass flash (English).
- 2026-09-27 — IA pack: https://archive.org/details/kf-300-flash-windows
- 2026-09-27 — removed agent brief; guide fully in English.
