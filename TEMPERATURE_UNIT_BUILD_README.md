# Temperature Unit Toggle - Build Summary

## Overview
Successfully implemented temperature unit conversion feature (Fahrenheit/MPH) for the CrossPet weather application.

## Compilation Status ✓

### Debug Build
- **File**: `firmware/crosspet-temp-unit-debug.bin`
- **Size**: 5.9 MB
- **Build Environment**: `default` (LOG_LEVEL=2)
- **Features**: Full serial logging enabled
- **Built**: March 10, 2026 @ 00:32 UTC

### Production Build  
- **File**: `firmware/crosspet-temp-unit-production.bin`
- **Size**: 5.8 MB
- **Build Environment**: `gh_release` (LOG_LEVEL=0)
- **Features**: Silent operation (no serial output)
- **Built**: March 10, 2026 @ 02:26 UTC

## Code Changes

### Settings Infrastructure (CrossPetSettings)
- Added `uint8_t useFahrenheit = 0;` field for temperature preference
- Updated JSON persistence (load/save in `crosspet.json`)

### UI Integration (SettingsActivity)
- Added "Use Fahrenheit (& MPH)" toggle in Display settings
- Setting synced with CrossPetSettings via dynamic getter/setter

### i18n Strings
- English: `STR_USE_FAHRENHEIT: "Use Fahrenheit (& MPH)"`
- Vietnamese: `STR_USE_FAHRENHEIT: "Dùng Fahrenheit (& MPH)"`

### Weather Display (WeatherActivity)
- Added conversion helper functions:
  - `celsiusToFahrenheit(float c)` → C * 9/5 + 32
  - `kmhToMph(float kmh)` → kmh * 0.621371
- Applied conversions to:
  - Main temperature display
  - "Feels like" temperature
  - Wind speed (with unit string change km/h ↔ mph)
  - 5-day forecast high/low temperatures

## Upload Instructions

### Method 1: Using PlatformIO (Recommended)
```bash
cd /home/deck/crosspet-ble/crosspet
pio run -e default --target upload --upload-port /dev/ttyACM1
```

### Method 2: Using esptool.py directly
```bash
esptool.py -p /dev/ttyACM1 -b 921600 --before default_reset --after hard_reset \
  write_flash -z --flash_mode dio --flash_freq 80m --flash_size 16MB 0x10000 \
  firmware/crosspet-temp-unit-debug.bin
```

### Method 3: Using the web flasher at https://xteink.dve.al/
1. Download the firmware binary from the releases
2. Go to https://xteink.dve.al/debug
3. Click "OTA Fast Flash" and select the binary

## GitHub Status

✓ All changes committed and pushed to: https://github.com/thedrunkpenguin/crosspet

Latest commits:
- `a6aaf79`: build: Add compiled firmware binaries (debug and production)
- `823dfbe`: feat: Add temperature unit toggle (Fahrenheit/MPH support)

Both firmware binaries are available in the `firmware/` directory and in the git repository.

## Testing Checklist

- [ ] Flash debug build to device
- [ ] Navigate to Settings → Display → "Use Fahrenheit (& MPH)"
- [ ] Toggle setting ON/OFF
- [ ] Launch Weather app and verify units change:
  - ON: Shows °F and mph
  - OFF: Shows °C and km/h
- [ ] Verify 5-day forecast temperatures convert correctly
- [ ] Restart device and verify setting persists

## Version Info
- CrossPet Version: 1.6.9
- Platform: ESP32-C3 (16MB Flash)
- Build System: PlatformIO 6.1.19

