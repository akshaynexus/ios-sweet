# iOS 16 on Samsung Galaxy A71 4G

This project began by forking the upstream iOS-on-SM7150 work and **adding Galaxy A71 4G support from 21 October 2026**. The A71-specific bring-up targets the Samsung Galaxy A71 4G (`SM-A715F`, Qualcomm `SM7150`).

> **Experimental bring-up, not a daily-driver OS.** iOS has booted on the A71 and the touchscreen and interface are usable, but the system is very laggy. Rendering was CPU/software-based, and GPU acceleration has not been confirmed.

## A71 status

- iOS reaches the lock/home screen and responds to touch.
- Touch input works through the Samsung **S6SY771** (`sec_ts`) controller at I²C address `0x48`; earlier references to Goodix were exploratory only.
- The project currently lacks a working GPU driver, Wi-Fi, Bluetooth, cellular, audio, camera, GPS, and complete iOS services.
- CPU rendering makes the interface noticeably slow and limits usable performance.

## A71 timeline

- The repository forked from the upstream iOS-on-SM7150 project on 21 October 2026.
- A71 support was added afterwards with Galaxy A71 hardware mappings and bring-up work.
- Historical Redmi Note 10 Pro / `sweet` information remains relevant only as upstream or shared-SM7150 context, not as a separate Galaxy A71 model claim.

## Installing the boot image

For the confirmed A71 workflow, install the generated `shim-boot.img` through **TWRP**:

1. Install TWRP for `SM-A715F`.
2. Copy `shim-boot.img` to the phone or push it through ADB:

   ```bash
   adb push shim-boot.img /sdcard/
   ```

3. In TWRP, open **Install → Install Image**.
4. Select `shim-boot.img`.
5. Choose the **Boot** partition.
6. Swipe to flash.

A Samsung `.tar`/Odin package is optional and is not the primary documented A71 workflow. Preserve recovery and backup the boot partition before installing anything.

## Project contents

- XNU patches and kernel build workflow
- A bare-metal boot shim and payload/image tools
- iOS root-image preparation tools
- A71 hardware bring-up and diagnostic documentation

## Documentation

- [Galaxy A71 port guide](docs/galaxy-a71-port.md)
- [A71 touchscreen investigation](docs/a71-touch-2026-10-22.md)
- [Detailed engineering notes](NOTES.md)
- [QEMU testing](docs/QEMU_TESTING.md)

## Build

Kernel builds use the repository’s GitHub Actions workflow. See [`.github/workflows/xnu.yml`](.github/workflows/xnu.yml) and the [A71 port guide](docs/galaxy-a71-port.md).

## Safety

This is experimental software. Back up Android and personal data, preserve recovery and partition backups, and do not flash or erase userdata unless you intend to replace Android data.

## Credits

Based on Apple’s open-source XNU and upstream iOS-on-SM7150 work, with Galaxy A71 4G hardware and bring-up contributions.
