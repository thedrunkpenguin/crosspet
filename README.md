# CrossPet Reader

**Your pocket e-reader — with a virtual chicken.**

CrossPet is a Vietnamese fork of [CrossPoint Reader](https://github.com/crosspoint-reader/crosspoint-reader) — open-source firmware for the **Xteink X4** e-paper reader, built with **PlatformIO** on **ESP32-C3**.

![](./docs/images/crosspet.png)

## Hardware

| Spec | Detail |
|------|--------|
| MCU | ESP32-C3 RISC-V @ 160MHz |
| RAM | ~380KB (no PSRAM) |
| Flash | 16MB |
| Display | 800×480 E-Ink (SSD1677) |
| Storage | SD Card |
| Wireless | WiFi 802.11 b/g/n, BLE 5.0 |

## What makes CrossPet special

### Virtual Chicken Companion
Your chicken grows with every page you read. Evolution system: Egg → Hatchling → Juvenile → Adult → Elder, with 3 variants (Scholar/Balanced/Wild) based on reading consistency. Hunger, happiness, and energy are affected by reading activity and care.

### Reading Experience
- **EPUB 2 & 3** with image support
- **Anti-aliased grayscale** text rendering
- **3 font families**, 4 sizes, 4 styles (including Bokerlam Vietnamese serif)
- **Multi-language hyphenation**
- **Reading statistics & streaks** — per-session, daily, all-time tracking
- **KOReader Sync** cross-device progress
- **4 screen orientations**, remappable buttons

### Sleep Screens
- **Clock** — 7-segment digital clock + calendar with lunar date and 28 rotating literary quotes
- **Reading Stats** — today's reading time, all-time total, current book progress
- **Cover** — book cover art display
- **Reliable refresh** — clock and stats update correctly on brief wakeups

### Tools
- **Weather** — current weather via Open-Meteo API
# CrossPet (Bluetooth HID fork)

This repository is a fork of [trilwu/crosspet](https://github.com/trilwu/crosspet) with expanded Bluetooth HID support for external page-turn controllers.

![](./docs/images/cover.jpg)

## Firmware Downloads (front page)

- **Debug capture firmware (for collecting HID logs):** [firmware/crosspet-bt-debug-capture.bin](./firmware/crosspet-bt-debug-capture.bin)
- **Production firmware (slim, logging disabled):** [firmware/crosspet-bt-production.bin](./firmware/crosspet-bt-production.bin)

### Releases

- Production builds are also published in **GitHub Releases** for one-click download from the release assets.
- Use the latest `crosspet-bt-production.bin` asset for normal use.

## What was added (Bluetooth implementation)

- New BLE HID manager in `lib/hal/BluetoothHIDManager.*`
- Device profile system in `lib/hal/DeviceProfiles.*`
- Bluetooth settings UI activity in `src/activities/settings/BluetoothSettingsActivity.*`
- Stable key injection path with startup noise gate, key cooldown, and profile-based key mapping
- Inactivity disconnect handling to keep long reading sessions stable
- Generic HID compatibility mapping for common keyboard/consumer/media page-turn keycodes
- Learn mode in Bluetooth settings to capture your remote's own Previous/Next keycodes

## Supported devices

### Confirmed working

- **MINI_KEYBOARD** (keypad profile)
- **GameBrick-style BLE controller** (D-pad profile)

### Likely to work / may be supported with profiling

- BLE HID remotes that send standard keyboard, consumer-page, or common media keycodes
- Generic page-turn controllers presenting as BLE HID keyboard

### Learn mode (for unknown remotes)

If your remote connects but does not turn pages correctly:

1. Go to **Settings → Bluetooth** and connect the remote
2. Select **Learn Page-Turn Keys**
3. Press your remote's **Previous Page** button, then **Next Page** button
4. Confirm to save (mapping persists for future sessions)
5. Use **Clear Learned Keys** in Bluetooth settings to reset

If your device connects but buttons do not map correctly, capture debug logs (below) and open a support request.

## How users can capture debug logs for new devices

1. Flash **debug capture firmware**: [firmware/crosspet-bt-debug-capture.bin](./firmware/crosspet-bt-debug-capture.bin)
2. Connect the X4 by USB-C
3. Start serial monitor:

```sh
pio device monitor --port /dev/ttyACM2 --baud 115200
```

4. On device: open **Settings → Bluetooth**, scan and connect your BLE remote
5. Press each remote button a few times
6. Copy log lines that include these tags:
	- `[INF] [BT] Scan device:`
	- `[DBG] [BT] HID Report (...)`
	- `[INF] [BT] >>> BUTTON PRESSED:`
	- `[INF] [BT] Matched profile ...` or unmapped key lines

## How to request support for a new BLE device

Open an issue in this fork and include:

- Device name + product link
- BLE MAC prefix shown in logs (first 3 bytes)
- Full button logs from debug firmware
- Which physical button should map to Page Forward / Page Back

With that info, a new profile can be added to `DeviceProfiles` and rolled into future firmware.

## Build variants

- `bt_debug_capture` (verbose logs enabled)
- `bt_production` (serial logging removed for slim production use)

Build commands:

```sh
pio run -e bt_debug_capture
pio run -e bt_production
```

## Flashing

### Web flasher

1. Connect X4 via USB-C
2. Go to https://xteink.dve.al/
3. In OTA/manual section, select one of the `.bin` files above and flash

### PlatformIO upload

```sh
pio run -e bt_production -t upload
```

Use `bt_debug_capture` instead when collecting logs.

## Upstream + contribution

- Upstream base: [trilwu/crosspet](https://github.com/trilwu/crosspet)
- This fork focuses on BLE HID compatibility and device onboarding workflow

CrossPet/CrossPoint is not affiliated with Xteink.

