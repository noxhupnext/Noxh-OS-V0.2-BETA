# Roadmap: from the PC/VM prototype to a Raspberry Pi handheld

## The key fact: a Pico cannot run this

The **Raspberry Pi Pico / Pico 2 (RP2040 / RP2350)** are *microcontrollers*: 264 KB / 520 KB of RAM, no MMU, no
Linux, no desktop graphics stack. NOXH OS as built here (Linux + Xorg + Python + SDL2 + a WebKit browser) needs
**hundreds of megabytes** of RAM and a real operating system. It **cannot be copied onto a Pico**, and nothing in this
repository pretends otherwise.

| Board | RAM | Can run this NOXH OS shell? | What it can do |
|---|---|---|---|
| Pico / Pico W (RP2040) | 264 KB | ❌ no | a *separate* "NOXH Lite" firmware: menu + a few tile/sprite games |
| Pico 2 / 2 W (RP2350) | 520 KB | ❌ no | same, a bit more room (320×240 16-bit framebuffer = 150 KB) |
| **Zero 2 W** | 512 MB | ⚠️ yes, reduced | shell + games, **no heavy browser**, 640×360 render mode recommended |
| **Pi 4 (2-8 GB) / Pi 5** | 2-16 GB | ✅ yes, full | everything incl. the browser; the recommended handheld target |
| Pi 3 / 3A+ | 1 GB / 512 MB | ⚠️ mostly | works, slower |

**Recommendation:** if you want the browser, rich graphics and broad game compatibility on a handheld, build it on a
**Raspberry Pi Zero 2 W (budget, no browser) or Pi 4 / Pi 5 (full)** – not on a Pico.
Keep the Pico for a deliberately small "NOXH Lite" if you love the microcontroller route.

## Track A – NOXH on Pi Zero 2 W / 4 / 5 (Linux)

What already helps: the shell is plain Python + SDL2 and every hardware touch is behind `shell/noxh/hal.py`.

1. **`PiHAL(LinuxHAL)`** – override only what differs:
   * `battery()` → fuel-gauge over I²C (MAX17048 / UPS HAT) or `/sys/class/power_supply`
   * `brightness()/set_brightness()` → backlight PWM via sysfs (`/sys/class/backlight`) or `pigpio`
   * `power_off()` → `systemctl poweroff` (already works) + `gpio-shutdown` overlay for the power button
   * radios: the `nmcli` / `bluetoothctl` code is already generic Linux
2. **Input** – GPIO buttons/joysticks: use the `gpio-keys` device-tree overlay (buttons → evdev keys) and `joypad`/`mk_arcade_joystick_rpi`
   or an I²C/SPI ADC + a small `uinput` daemon, so they show up as a normal gamepad. NOXH's `inputs.py` then works unchanged and
   Settings → Controls remaps them.
3. **Video without X** – replace Xorg+openbox with SDL2's **KMS/DRM** backend (`SDL_VIDEODRIVER=kmsdrm`). Saves ~100 MB RAM
   and boot time. Launching external apps then needs a tiny compositor-less launcher (or `cage` / `weston --kiosk`).
4. **Resolution** – the UI is laid out at a logical 1280×720. Add a `NOXH_LOGICAL=640x360` render mode (draw at half size, scale up)
   for small screens (e.g. 800×480 / 640×480 DSI panels) and the Zero 2 W. *(Planned – not implemented in v0.1.)*
5. **Image build** – port `build-iso.sh` to **arm64**: either `pi-gen` (Raspberry Pi OS Lite base) or Buildroot; produce a `.img`
   for an SD card with a writable data partition labelled `NOXHDATA` (the existing persistence logic then works as-is).
6. **Audio** – I²S DAC / PWM overlay + ALSA; the shell already selects ALSA cards.
7. **Browser** – Pi 4/5: GNOME Web (Epiphany) or Chromium kiosk; Zero 2 W: skip, or NetSurf for basic pages.
8. **Performance budget** – profile with the built-in 60 fps target; keep "Animation intensity: Low/Off" and static backgrounds as the
   defaults on Zero 2 W.

### Milestones
| # | Goal | Done when |
|---|---|---|
| M1 | arm64 build of the shell in a stock Raspberry Pi OS | `python -m noxh.main` runs on a Pi 4 with HDMI + USB pad |
| M2 | KMS/DRM mode, no X | boots to NOXH in < 15 s on Pi 4 |
| M3 | `PiHAL` (battery, backlight, power button) + GPIO controls | handheld prototype on a breadboard |
| M4 | 640×360 render mode + Zero 2 W profile | 60 fps in the 5 built-in games |
| M5 | Custom SD image (`pi-gen`) with persistence partition | flash-and-play image |
| M6 | Enclosure / hardware revision, thermal & battery tuning | daily-usable handheld |

## Track B – "NOXH Lite" for Pico 2 (separate firmware)

A different code base (C/C++ with the Pico SDK, or MicroPython/CircuitPython for prototyping):

* 320×240 SPI LCD (ST7789/ILI9341), 16-bit RGB565, **tile/sprite engine** (no full-screen alpha effects)
* menus: home tiles, settings (volume, brightness, button test), simple profile (name + high scores in flash/LittleFS)
* ports of **Flap, Blocks, Invaders, Maze, Racer** (they are simple enough; the rules are in `shell/noxh/games.py`)
* GPIO d-pad + 4-6 buttons (debounced, remap table in flash), PWM/I²S audio, SD card for extra games
* Pico W / 2 W: Wi-Fi status & firmware update only – **no browser**, no multi-GB games, no emulators beyond tiny 8-bit cores
* reuse from this project: asset ideas, game rules, UI layout, `hal.py` *interface names* (battery, brightness, power, input) as a design contract

## Shared design rules (already followed in v0.1)
* UI never touches hardware directly → everything through `HAL` (`hal.py`).
* Game library, profiles, settings, control mappings are plain JSON → easy to carry across platforms.
* Every unavailable feature reports `available: False` + a reason, so ports can degrade gracefully.
