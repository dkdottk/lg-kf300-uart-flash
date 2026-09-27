# LG KF300 — UART flash with GSM-Multi

**Status: SUCCESS (Pass), 2026-09-27.** The phone powered on by itself after Pass.  
Do **not** suggest “unplug USB to turn the phone on.” **5V on R101 is a pulse only** — do not leave it connected from the start.

Internet Archive pack: https://archive.org/details/kf-300-flash-windows  
GitHub (docs only): https://github.com/dkdottk/lg-kf300-uart-flash

---

## 1. Why we flashed

- Menus worked, but folders / gallery / games → lag → reboot (corrupt FS/NAND).
- USB BootROM was unreliable.
- Path: **UART + GSM-Multi V3.0**, ADI **Hermes (AD6527)**.
- CN300 connector ripped → solder to R310 / R311 / R101. Type‑C **removed** for flash (VBUS → USB_DET breaks the UART path).

---

## 2. Files

| Where | Path |
|-------|------|
| Internet Archive | https://archive.org/details/kf-300-flash-windows |
| Windows pack (example) | `Documents\KF300_Flash_Windows\` |
| GSM-Multi install | `C:\GSMULTI\` |
| DLL | `...\2_DLL\KF300_080313.dll` |
| Firmware | `...\3_Firmware\KF300AT-00-V10l-CIS-XXX-APR-17-2008.bin` |

CH340 COM port in the successful session: **COM5** (yours may differ).

SHA256:

```
KF300_080313.dll
  a390cf365cf8b95f11727a7ce912b412a496cf669634fdee601d197fd2575ef2
SETUP_GSMULTI_V30.exe
  c69b70a3406efd79bf20818c5a4491d18f217f29d3a75d6a10a5fd111606c397
KF300AT-00-V10l-CIS-XXX-APR-17-2008.bin
  d16da46b3c3e156f830fc7af56fc56d736e76e7d74be8fe95aa6fcc7457415c6
```

Also see [SHA256.txt](./SHA256.txt). Scan the `.exe` on VirusTotal before running.

---

## 3. GSM-Multi configuration

| Field | Value |
|-------|-------|
| DLL | `KF300_080313.dll` |
| S/W | `KF300AT-00-V10l-CIS-XXX-APR-17-2008.bin` |
| Port | **UART** |
| Baud | **921600** |
| Start Com = End Com | **one** CH340 COM (not 1–16 — you will miss the Hermes window) |
| ADI boot | **Hermes (AD6527)…** — select explicitly in the UI |

CH340 logic jumper = **3.3V**. Do **not** wire the module 3.3V/VCC pin to the phone. Phone power = battery only.

In `C:\GSMULTI\config.ini`, set Start/End Com to a single port. If Multi is already open it keeps the old config in RAM — **fully quit** and reopen.

---

## 4. Pinout (CN300, service schematic SVC ENG_080222)

| CH340 | Phone |
|-------|-------|
| GND | GND / shield |
| RXD | **R310** = phone TX (was pin 16), CPU side of the resistor |
| TXD | **R311** = phone RX (was pin 17), CPU side of the resistor |
| 5V | via **47 kΩ** (not 47 Ω) → **R101 / EXT_PWRON** (was pin 11) |
| 3.3V | do not connect |

Do not confuse with VE-Pro / Xintel **box** pin numbers.  
Mac check: `probe.py -b 921600` → `boot: 1B` with correct orientation; after TX/RX swap → 0.

---

## 5. Working procedure (verified)

**5V on R101 = PWRON pulse (0→5V edge), not constant power from step 1.**

1. Plug USB CH340 into the PC (**do not unplug** until Pass). Type‑C removed.  
2. Connect **everything except 5V**: GND + RXD→R310 + TXD→R311. Keep the **5V–47k wire off R101**.  
3. Multi → **Start** → **Waiting / Wait Phone Connecting…**.  
4. **Insert the battery** (still no 5V).  
5. **Pulse 5V**: touch/solder 5V–47k onto R101.  
6. When **ramloader / %** starts → **do not touch anything** until **Pass / OK / Success**.  
7. Stop → disconnect wires → USB.

After Pass the phone powers on by itself. First boot may be slow (logo, reboots, FS rebuild) — **do not pull the battery**. Remove UART for normal use. Check folders / gallery / games.

Watch the **Multi COM log**, not the LG logo: the Hermes window is ~1 byte in the first fraction of a second; the logo means that window already passed.

---

## 6. Why other sequences failed

| Mistake | Why |
|---------|-----|
| 5V already on R101, then insert battery | no 0→5V PWRON edge |
| Unplug/replug USB | phone turns on (5V edge) but Windows drops COM — Multi never hears boot |
| Long Power with 5V permanently on / after a failed NAND write | often useless → battery out for ~10 s |
| Flip TX/RX “so the screen wakes” | idle 3.3V lands on phone TX; Mac shows boot 0; **wrong** orientation |
| Disconnect TX/RX “so it turns on” | screen may wake; Multi stays Waiting forever |
| Pull TX/RX or battery during % | ramloader write error → battery out ~10 s, then the working order again |
| Start Com=1 … End=16 | miss the short Hermes window |

Idle CH340 TXD (3.3V) on R311 can bias RX; still use RXD→R310, TXD→R311 — catch the window with the 5V pulse, not by flipping wires.

---

## 7. Measurements (don’t chase ghosts)

- Module 5V pin ≈ 4.9V to module GND = normal USB VBUS for the CH340; do not feed that pin straight into the board without 47k to R101.  
- TX/RX idle DC ≈ 3.3V. “8V on RX” is usually a meter mode / reference mistake.  
- Ohmmeter on 5V–GND / RX–GND on a live board is meaningless.  
- You do not need an ohmmeter to flash.

CH340 LEDs: trust **TXD/RXD pin labels**, not LED color. At start, **RX** should blink (phone sends boot), then **TX** if Multi answers. TX only, no RX ⇒ wires swapped. A steady glow just from plugging wires means nothing.

---

## 8. Do not

- Reflash if you already have Pass and menus work.  
- Wire CH340 3.3V to the phone.  
- Connect 5V without 47k (and never 47 Ω).  
- Leave TX/RX swapped.  
- Close Multi or yank USB/wires while percentages run.  
- Treat “unplug USB to power on” as a flash method.

---

## 9. If you need to flash again

Strictly: **UART without 5V → Waiting → battery → 5V pulse on R101.**

Sources: [unlockers.ru archive t-19971](https://www.unlockers.ru/archive/index.php/t-19971.html); [Chinese UART + MultiGSM text guide](http://shouji.pc004.com/xuangou/2009/09/08/2185552.shtml)

---

## 10. macOS (link check only)

```bash
python3 probe.py -b 921600
```

Full flash is not possible from macOS alone — use Windows or a VM with Multi + USB passthrough of the CH340.

---

## Changelog

### 2026-09-27
- Added: Pass status; working order with 5V pulse after battery.
- Changed: constant-5V / Power-only sequences documented as dead ends.
- Changed: guide translated to English; agent brief removed from the repo.
- Added: Internet Archive + GitHub links.
