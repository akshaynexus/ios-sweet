# iOS 16 on Samsung Galaxy A71 4G

This project ports Apple’s iOS 16.7.1 userland and XNU kernel to the Samsung Galaxy A71 4G (`SM-A715F`, Qualcomm `SM7150`).

> **Experimental bring-up, not a daily-driver OS.** iOS has booted on the A71 and the device owner has confirmed that the touchscreen and interface are usable. The system is very laggy: rendering was being done on the CPU, and GPU acceleration has not been confirmed.

## A71 status

- iOS reaches the home screen and its interface responds to touch.
- The owner reports being able to use the phone’s interface and features, with substantial lag.
- The touch controller identified in the project’s A71 notes is Samsung **S6SY771** (`sec_ts`) over I²C at address `0x48` — **not Goodix**. Goodix appears in earlier exploratory code, but it is not the controller identified for the confirmed A71 touch setup.
- Rendering was CPU/software-based in the reported boot; Adreno 618 GPU work is experimental and does not establish hardware-accelerated iOS graphics.

This is a hardware-specific research project. The state of individual drivers and features may change; see the linked engineering notes for detailed test results and caveats.

## Project contents

- XNU patches and kernel build workflow
- A bare-metal boot shim and payload/image tools
- iOS root-image preparation tools
- A71 hardware bring-up and diagnostic documentation

## Documentation

- [Galaxy A71 port guide](docs/galaxy-a71-port.md)
- [A71 touchscreen investigation](docs/a71-touch-2026-10-22.md)
- [Detailed engineering notes and latest handoff](NOTES.md)
- [QEMU testing](docs/QEMU_TESTING.md)

## Build

Kernel builds use the repository’s GitHub Actions workflow. See [`.github/workflows/xnu.yml`](.github/workflows/xnu.yml) and the [port guide](docs/galaxy-a71-port.md) for project details.

## Safety

This is experimental software. Do not use it on a device containing data you need to keep. Preserve recovery, bootloader, modem, persist, and partition backups. Keep device dumps, serial numbers, and Apple firmware artifacts out of Git.

## Credits

Based on Apple’s open-source XNU and related projects, with contributions from the upstream iOS-on-SM7150 work and the Galaxy A71 port.
