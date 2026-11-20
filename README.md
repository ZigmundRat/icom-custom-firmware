# Icom custom firmware (IC-705 / IC-9700)

Custom firmware releases for the Icom IC-705 and IC-9700 by Dawid SQ6EMM: transverter
support (TRV1–TRV8, RF display up to 122 GHz, CI-V frequency translation, per-transverter
max TX power, TX delay and PTT inhibit) and an A/B bootloader that keeps the previous
firmware in the radio and lets you switch back.

**Web patcher:** https://sq6emm.github.io/icom-custom-firmware/

**Discussion, questions and reports:** the official place is the forum thread
https://mikrofale.cafe/showthread.php?tid=1296

No Icom firmware is distributed here. You download the official `.dat` from Icom yourself;
the patcher (running entirely in your browser — the file never leaves your computer) applies
the patch and offers the result **only if it is byte-identical to the build that was tested**
(MD5 checked on input and output). There is always exactly one patch per official file: the
latest release.

| Official file | Release | Output MD5 |
|---|---|---|
| IC-9700 `9700E150.dat` (`6f04fd2248ba39254ed01b30b52ce36a`) | 20261119v14 (install together with SD apps 20261119v14) — TRV per MAIN/SUB (DUAL, SAT) with per-transverter ATT / P.AMP, scope dB/div, A/B bootloader, ICAP decoders (Q65/FT8/JT4/PI4/WSPR), beacon, QSO mode (FT8/Q65/JT4), APRS GPS TX mode, PSKReporter | `348d1d6118c94e05df0b3027f2dbd103` |
| IC-705 `705E140.dat` (`2c9c1d7f3fc80ef173e01d841cd1248a`) | 20261119v14 (with SD apps 20261119v14) — TRV with per-transverter ATT / P.AMP, scope dB/div, A/B bootloader, extended TX ranges, ICAP decoders, beacon, QSO mode (FT8/Q65/JT4), APRS GPS TX mode, PSKReporter | `70af0dea75a3b6cc3c555cbe35d21c36` |
| IC-705 `705J140.dat` (`b605171288db4a74e0992a8f2afcb1c3`) | 20261119v14 (with SD apps 20261119v14 for J) — same features as the E release (TRV with per-transverter ATT / P.AMP, scope dB/div, A/B bootloader, ICAP decoders, beacon, QSO mode, APRS GPS TX mode, PSKReporter); installation confirmed on an IC-705 J. Extended TX limits are set, but a J gives no output power on 2 m above 146.000 MHz (not fixed yet) | `1084dcdc5180e561394a50759725f04a` |

The IC-705 releases extend the TX frequency ranges beyond the amateur bands. You alone are
responsible for transmitting only where your licence and national regulations allow.

## Release 20261119v14 (IC-705 E, IC-705 J, IC-9700)

What is new since 20261119v13:
- **QSO mode verified on the IC-705 E and the IC-9700** (FT8, Q65, JT4). A tap on the `TX?` line that comes too
  early now shows `QSO: wait 1 s, tap again` instead of being ignored.
- **Longer hold for CQ:** the step from the red (beacon) to the green (CQ) frame takes about three times longer.
- **`TX refused: <reason>`** on the DECODE screen names the condition that blocks a transmission.
- **APRS GPS TX mode:** MENU > SET > GPS > GPS TX Mode has a 4th choice, APRS — 1200 Bd AFSK position beacon on the
  current frequency (FM only), received stations go to RX History and `Apps\APRS.LOG`. `APRS.APP` is in the SD-apps
  bundle; the firmware starts and stops it by itself with the menu choice.

How to use it:
1. Download the official `.dat` from Icom, open the web patcher, select the file and download the patched one
   (or use the command line below). Install it with Icom's normal SD-card firmware update.
2. Download the SD-apps bundle for your radio from the web patcher page and copy **all** its files to
   `\IC-705\Apps\` or `\IC-9700\Apps\` on the SD card (IC-9700: power-cycle afterwards). Firmware and bundle
   must have the same release number.
3. Set your callsign and locator on the radio. For transmitting: USB-D mode, DATA MOD = WLAN (IC-705), SD card
   inserted, no remote (LAN) session; the app waits until the radio clock is locked.
4. MENU > SET > Apps starts an app; decodes appear on the DECODE screen.
   - Beacon: in a decoder mode, hold the mode tile in the emission popup until the red frame appears.
   - CQ: keep holding until the frame turns green.
   - QSO: tap a decoded line, wait about 1 s, tap the `TX?` line. One message per tap. PTT, EXIT or TRANSMIT stop at once.
5. APRS: set GPS TX Mode to APRS, tune an FM channel (e.g. 144.800 MHz in Europe), set the interval in GPS Auto TX;
   the beacon needs a valid position (GPS or manual). Turn **NR (Noise Reduction) off** on that receiver: NR distorts
   the AFSK tones and nothing is decoded.
6. QSOs are appended to `Apps\ICAPQSO.ADI` (ADIF). Spots go to PSKReporter automatically when call, locator and
   network are set.

Make the first transmissions on a dummy load.

APRS® is a registered trademark of APRS Software and Bob Bruninga, WB4APR.

## Command line

```sh
python3 icom_patch.py patches/9700E150_20261119v14.json 9700E150.dat 9700_20261119v14.dat
python3 icom_patch.py patches/705E140_20261119v14.json 705E140.dat 705E_20261119v14.dat
python3 icom_patch.py patches/705J140_20261119v14.json 705J140.dat 705J_20261119v14.dat
```

## Before you use it

- This is **not Icom software**. Use at your own risk; it may void your warranty.
- Releases that include the custom bootloader rewrite the radio's bootloader once during
  the SD-card update. A power loss at that moment can require recovery with a flash
  programmer — use a stable power supply.
- IC-9700 slot switch: hold POWER while connecting the DC supply (~5 s) to boot the other
  firmware slot. IC-705: hold POWER at power-on until the LED blinks (~3 s).
- Before connecting a transverter, verify TX delay (scope SEND vs RF) and output power on a
  dummy load.
- You alone are responsible for transmitting only where your licence and national
  regulations allow.

## Repository layout

- `docs/` — the web patcher (GitHub Pages).
- `patches/` — release patches; `releases.json` — which patch is the current release for
  each official file; `tools/build_index.py` regenerates `docs/patches/index.json`.
- `icom_patch.py`, `ic_rza1_repack.py`, `icom_rza1_decode.py` — command-line patcher.
- Reverse-engineering tools and format documentation:
  https://github.com/sq6emm/ic705-ic9700-ic905-bring-your-own-firmware

License: MIT (see `LICENSE`).
