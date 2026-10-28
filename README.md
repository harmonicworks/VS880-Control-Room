# VS-880 Control Room

A browser front-end for the **Roland VS-880** (V-Xpanded, with the VS8F-1 effect board). It makes the inside of the machine visible: both effects with every parameter on a knob, the full mixer with EQ and routing, and the song settings that usually hide behind menus and the jog wheel.

**Use it:** open the page in Chrome or Edge (Safari has no Web MIDI). Everything runs in your browser; nothing is uploaded.

## Status
Personal project, built for my own studio and shared as-is under the MIT licence. Free to use, copy and change.
It is **not actively maintained**: I update it only when I run into something while using it myself. No support, no feature requests, no update schedule.

## What it does
**Effects (FX1 + FX2)**
- Reads the loaded patch and shows every parameter of all 20 VS8F-1 algorithms as knobs, in the VS-880's own menu order
- Drag, scroll or type values; each knob can learn a knob or fader from a MIDI controller (tested with a Korg nanoKONTROL2), with its own range and pickup
- Preset browser for banks A, B and U (with names), rename, revert, bypass, and a red **Safe** button that loads a harmless delay
- Snapshot A/B with a morph fader
- Keep patches in a library in your browser, or save/open them as `.syx` files

**Mixer & routing**
- 8 channel strips + master that follow the VS-880 live
- All three mixer modes (Input → Track, Input Mix, Track Mix), each with its own channels
- Track status, FX1/FX2 send mode, level and pan (including the "one insert per effect" rule), 3-band EQ, pan, faders, AUX master

**Under the hood**
- Song list with sample rate, record mode and protect
- Current song: rename, protect, tempo, MTC type, V-track per track

**Lab tools** (switch on at the top): raw SysEx read/write, Program Change test, memory scanner and memory compare, log.

## Setting up the VS-880
SYSTEM → MIDI PRM: **MIDIThr = Out · SysEx.Rx = On · SysEx.Tx = On · CtrType = Excl · P.C.Eff = On · Device ID 17**

Good to know:
- These MIDI settings are stored **per song**. A new song starts with SysEx off, so set them again (or start new songs from a template song).
- Don't open the MIDI settings while creating a new song; my VS-880 hung (a restart fixed it).
- Nothing becomes permanent until you store it on the VS-880 yourself: **WRITE** for effect patches (U bank), **SHIFT + STORE** for songs.
- Reverb and Gate Reverb only run on Effect 1. Sent to Effect 2, the VS-880 overloads with a loud beep. The page blocks this, and Safe fixes it if it ever happens.

## How it works (short version)
- Roland SysEx, model ID `7C`: write `F0 41 10 7C 12 [address] [data] [checksum] F7`, read `F0 41 10 7C 11 [address] [size] [checksum] F7` (the VS-880 only answers 128-byte requests)
- `04 00` / `04 01` = FX1 / FX2 patch: algorithm, 12-character name, switches, then 2-byte parameters
- `02 00`–`02 05` = mixer: most settings are rows of 24 values = 3 mixer modes × 8 channels; EQ uses 25 steps with 64 as centre
- `01 00` = current song (name, sample rate, mode, protect) followed by the disk's song list
- `00 00` = song/system settings (tempo, MTC type)
- Effect banks: Bank Select **CC 32** (A = 0, B = 1, U = 2) + Program Change on channel 1 (FX1) or 2 (FX2)

## Not possible (as far as I found)
- Writing directly into the U bank (loading works; storing is done on the machine)
- Reading the time / measure display or remaining disk time
- Audio from the disk

## Thanks
- The **untidymusic VS880 Ctrlr panel**, whose messages were the first map into the VS-880
- Roland's VS-880, VS-880EX and VS8F-1 manuals for parameter names and ranges

## How this was made
Built over one long day and evening with Claude (Anthropic's AI), which did most of the reverse-engineering and coding from dumps and captures I made on my own VS-880. Tested on real hardware.

MIT licence.
