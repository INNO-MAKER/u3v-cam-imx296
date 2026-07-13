# U3V Camera SDK — Release Notes

**Version:** 2.2.3
**Date:** 2026-07-13
**Type:** Maintenance release (ABI-compatible)

---

## What's New

### Windows: bundled WinUSB driver installer

The Windows package now ships the WinUSB driver installer alongside a
first-run installation guide. On a new Windows machine, install the
camera driver directly from the package — no separate download.

```
u3v-sdk-2.2.3-windows-x64/
├── WINUSB_DRIVER_INSTALL.md   (step-by-step guide)
└── tools/
    ├── zadig-2.9.exe          (driver installer)
    ├── LICENSE-zadig.txt
    └── README-driver.txt
```

### External hardware trigger usage guide

New document `TRIGGER_USAGE.md` in the package root covers external
trigger integration end-to-end: wiring, recommended pulse width, host-
side code for Arduino, Raspberry Pi, NVIDIA Jetson, STM32, TTL function
generators, and 24 V PLC outputs, plus the SDK-side `TriggerXxx`
configuration in C and Python.

### Lower viewer CPU usage on Linux (especially Raspberry Pi 5)

Live view in `u3v_viewer` is substantially lighter on CPU for single-
camera streams. The display is now painted at ~30 fps regardless of
the sensor's frame rate, which is smooth to the human eye and roughly
halves the per-frame rendering cost on ARM64 hosts. Sensor capture
continues at its configured rate, so callbacks, frame counters, and
dropped-frame statistics remain unchanged.

## Compatibility

**Drop-in binary replacement — no source changes, no relink required.**

- C/C++: replace `libu3v_cam.so.2.2.2` with `libu3v_cam.so.2.2.3`
  (SONAME `libu3v_cam.so.2` unchanged). On Windows, replace
  `u3v_cam.dll`. On macOS, replace `libu3v_cam.dylib`.
- Python: replace the `u3v-sdk-2.2.3-python.zip` package.

All existing 2.1.x / 2.2.x application code continues to work unchanged.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.2.3-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.2.3-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.2.3-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.2.3-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.2.3-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.2.3-python.zip` |

For installation and usage instructions, see the package README or
contact the SDK provider. For a summary of improvements across earlier
versions, see `WHATS_NEW.md`.
