# NOXH OS 0.2 "Aurora"

Your own game-console operating system. You get **one file – `noxh-os.iso`** – that starts a full-screen
console interface (boot animation, setup wizard, profiles, game library, 5 built-in games, app installer,
file explorer, settings). It is not a website and not an Electron app: it is a real bootable system.

> **Who is this ISO for?** PCs and **VirtualBox** (Intel/AMD, "x86-64").
> It **cannot boot on a Raspberry Pi** (Pis use a different CPU type). See [Raspberry Pi](#raspberry-pi-3-read-this-before-flashing).

![Home](docs/screenshots/11_home.png)

---

## 1. Try it in VirtualBox (10 minutes)

**You need:** Windows PC, [VirtualBox](https://www.virtualbox.org) installed, the file `noxh-os.iso`.

1. Open VirtualBox → click **New**.
2. Fill in:

   | Box | Put this |
   |---|---|
   | Name | `NOXH OS` |
   | ISO Image | choose `noxh-os.iso` |
   | Type / Version | **Linux** / **Ubuntu (64-bit)** |
   | ☑ **Skip Unattended Installation** | **tick it** (important!) |
   | Base Memory | **3072 MB** (2048 is the minimum) |
   | Processors | **2** |
   | Hard Disk | ☑ *Do not add a virtual hard disk* (you can add one later, see §6) |

3. Click **Finish**, then select the VM → **Settings** and set:
   * **Display → Video Memory: 128 MB**, **Graphics Controller: VMSVGA**, 3D Acceleration: **off**
   * **Audio → Controller: Intel HD Audio**
   * **Network → Adapter 1: NAT** (cable connected)
4. Click **Start**. Wait ~30 seconds. You will see the NOXH logo animation, then the setup wizard.
5. Press **Right-Ctrl + F** for fullscreen.

**The mouse is "captured" by the VM?** Press the **Right Ctrl** key to get it back.

### First launch
Follow the wizard (Enter = next, **Skip** for optional steps): language → display → controller → network →
**create an account** (username + password, stored only on this machine) → **profile** (name, avatar, theme) → done.

---

## 2. Controls

| Do this | Keyboard | Xbox / generic pad | PlayStation pad |
|---|---|---|---|
| Move | Arrow keys | D-pad / left stick | D-pad / left stick |
| Select | **Enter** | A | ✕ |
| Back | **Esc** | B | ○ |
| Options (favorite, menu…) | **X** | X | □ |
| Search / "find apps" | **Y** | Y | △ |
| Next / previous tab | **E** / **Q** | RB / LB | R1 / L1 |
| **Quick Menu** (anywhere, even in games) | **F1** | Guide button | PS button |
| Pause a game | **P** | Start | Options |

The mouse works everywhere too. Every key/button can be changed in **Settings → Controls**
(and a "Restore defaults" button fixes mistakes). Arrow keys + Enter + Esc *always* work in menus.

---

## 3. Games vs Media (like a PS5)

The **Library** tab has a **Games / Media** switch (first button on the top row). Anything you add can be filed
under either one: in **Add**, choose *Show it under*; in the file explorer choose *Add to menu as a Game / as Media*;
or press **X** on any item → *Move to Media / Games*.

Five games are built in (**NOXH Flap, Maze, Racer, Blocks, Invaders**) – playable immediately, scores saved per profile.

![Library](docs/screenshots/16_library.png)

---

## 4. Install more apps (RetroArch, YouTube, …)

Open the **Apps** tab. Pick an app → **Install** (needs internet; in VirtualBox "NAT" is enough).
It then appears in your Library.

| App | What it is |
|---|---|
| **RetroArch** | emulator hub (NES, SNES, Game Boy/Color/Advance, Mega Drive, PC Engine, DS, PS1) – **you supply your own game files** |
| ScummVM, DOSBox | classic adventure / DOS games |
| SuperTuxKart, OpenTTD, Neverball, Frozen Bubble | free games |
| Kodi, mpv | media center / video player |
| **YouTube (TV layout)**, YouTube, YouTube Music, Twitch, Internet Archive Arcade, itch.io | web apps – open in the built-in browser |

* **Add web app** – make a tile for any website. **Install by name** – install *any* Ubuntu package by name (advanced).
* For apps that don't understand game pads (browser, YouTube, ScummVM, Kodi) NOXH automatically turns your pad into a
  mouse/keyboard: *left stick = mouse, A = click, B = back, D-pad = arrows, Start = play/pause,
  hold Start+Select 2 s = close the app.*
* Netflix / Disney+ / Spotify need DRM (Widevine) and **won't work**.
* YouTube video smoothness depends on the machine (inside VirtualBox it is fine for 360p–720p).

![Apps](docs/screenshots/50_apps.png)

### "I installed an app / I have a Linux program – how do I get it in my menu?"
Open **Files** (Home → Files, or Library → **Files**). Select any file with a green **APP** tag
(Linux programs: ELF files, AppImages, `.sh`/`.py` scripts, `.desktop` launchers) → **Add to menu as a Game / as Media**.
Two helper buttons:
* **Add installed Linux apps** – lists every installed app on the system; tick the ones you want, press **Y**.
* **Find apps in this folder** – scans a folder (e.g. a USB stick) for programs.

Apps that need a setup wizard (RetroArch etc.) simply open their own setup the first time you launch them.
*Windows `.exe` files:* only work if **Wine** is installed (PC only); NOXH tells you if it can't run something.

![Files](docs/screenshots/52_files.png)

---

## 5. Wi-Fi and Bluetooth

**Settings → Connectivity → Wi-Fi networks / Bluetooth devices** shows nearby networks/devices, lets you connect, pair,
and disconnect. If your machine has no Wi-Fi/Bluetooth hardware (VirtualBox doesn't), NOXH shows an
**"isn't available"** screen that explains why and what to do. In VirtualBox your internet is the **wired** (NAT) connection and
already works. For Wi-Fi/Bluetooth in a VM, attach a USB adapter: VirtualBox menu **Devices → USB**.

**Settings → Device Info** shows the NOXH version, your hardware, memory, storage, IP address, etc.

![Wi-Fi](docs/screenshots/56_wifi_unavailable.png)

---

## 6. Keep your stuff after shutdown (important in VirtualBox!)

The ISO runs from RAM, so **without this step every reboot starts the setup again.**

1. Shut the VM down. VirtualBox → Settings → **Storage** → click *Controller: SATA* → **Add Hard Disk** → *Create* → VDI, dynamic, **8 GB**.
2. Start NOXH → **Settings → Storage → "Set up persistent storage on /dev/sdb"** → confirm. NOXH formats that empty disk and restarts.

From then on profiles, library, settings, downloads **and installed apps** survive reboots (apps are re-installed from a cache on the disk at boot, so boot takes a little longer).

**Getting your own game files into the VM:** VirtualBox → Settings → *Shared Folders* → add a folder, tick *Auto-mount*. It shows up in NOXH's Files under **USB & media** (`/media/sf_…`). You may need to restart the VM once.

---

## 7. Run it on a real PC (Rufus)
1. Rufus → select a USB stick → **SELECT** → `noxh-os.iso`.
2. Partition scheme **GPT** (UEFI PCs) or **MBR** (old BIOS PCs). If Rufus asks, choose **"Write in DD Image mode"**.
3. Boot from the USB stick (usually F12 / F10 / Esc at power-on). Choose "NOXH OS".
Wi-Fi may need firmware that is not included (a USB Wi-Fi adapter or cable always works).

## Raspberry Pi 3 – read this before flashing
**This ISO will not boot on a Pi 3**, no matter whether you use Imager or Rufus: a Pi uses an ARM processor, this ISO is for
Intel/AMD. The NOXH program itself is Python and is built to be ported (all hardware access lives in `shell/noxh/hal.py`);
a **Pi 3 build is a separate piece of work** (arm64 image + GPIO button input + 720p performance mode). The plan,
hardware notes and why a Pico can't run this are in `docs/ROADMAP-RASPBERRY-PI.md`. A Pi 3 has 1 GB RAM, so expect no heavy
apps/YouTube HD there. For now, test everything in VirtualBox – it is the same interface.

---

## 8. Troubleshooting

| Problem | Fix |
|---|---|
| VirtualBox shows "No bootable medium" | Settings → Storage → empty optical drive → choose `noxh-os.iso`. |
| Black screen after boot menu | Reboot, pick **"NOXH OS – safe graphics"** in the blue menu. Make sure Graphics Controller = **VMSVGA**. |
| Very slow | Give the VM 3–4 GB RAM and 2 CPUs; Settings → Appearance → *Animation intensity: Low*, *Animated background: None*. |
| Setup wizard appears every boot | You haven't done §6 (persistence). |
| No sound | VM Settings → Audio enabled, Intel HD Audio; NOXH Settings → Audio (volume / output device). |
| "No internet" | VM Network → Adapter 1 = **NAT**, "Cable connected" ticked. |
| Mouse stuck inside VM | Press **Right Ctrl**. |
| Typed wrong password | 5 wrong tries lock for 30 s. Forgot it? Settings → System → Factory reset (erases NOXH data only). |
| An app says "isn't installed" after reboot | Without persistence apps vanish at reboot (§6). With it, wait for boot to finish re-installing. |
| Controller not detected in VM | VirtualBox → Devices → USB → tick your controller. Test in Settings → Controls → *Controller test*. |

## 9. What is and isn't finished (v0.2)
* ✅ Boots in VirtualBox/QEMU, wizard, accounts (hashed passwords), profiles + PIN, library, 5 games, Apps, Files, Browser launcher, Settings (9 areas + Device Info), Wi-Fi/Bluetooth screens, downloads with checksum verification, factory reset.
* ⚠️ Tested in a PC emulator and headless UI tests – **not yet** with a physical gamepad, real Wi-Fi/Bluetooth, or real hardware.
* ⚠️ Internet-dependent features (installing RetroArch etc.) were not exercised against the live Ubuntu servers from inside the ISO during testing; if one fails, the app screen shows the apt log.
* ❌ No installer to a hard drive (it's a live system + persistence), no online store/accounts (local only), only the main menu labels are translated.

## 10. For developers
`./build-iso.sh` rebuilds the ISO (Ubuntu 24.04, ~20 min, needs root on Ubuntu/WSL). Docs: `docs/ARCHITECTURE.md`,
`docs/ROADMAP-RASPBERRY-PI.md`. Quick UI testing without a VM: `pip install pygame numpy; cd shell; python -m noxh.main --windowed`
and `python tools/shots.py out/` (automated test + screenshots of every screen).
