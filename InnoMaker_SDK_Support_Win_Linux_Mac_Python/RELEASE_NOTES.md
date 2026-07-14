# U3V Camera SDK — Release Notes

**Version:** 2.2.5
**Date:** 2026-07-14
**Type:** Maintenance release (ABI-compatible)

---

## What's New

### Read back trigger and exposure settings

The SDK now provides functions to read back the values you configure, so
your application can query the camera's current state directly:

```c
u3v_camera_get_exposure_auto(cam, &enable);
u3v_camera_get_trigger_activation(cam, &activation);
u3v_camera_get_line_debounce(cam, &time_us);
u3v_camera_get_strobe(cam, &duration, &delay, &pre_delay);
```

The Python interface exposes the same values as properties and methods
(`camera.trigger_activation`, `camera.get_exposure_auto()`,
`camera.get_line_debounce_us()`, `camera.get_strobe()`).

### Named constants for trigger configuration

Trigger settings can now be written with descriptive names instead of
numeric values, available from `<u3v/u3v_types.h>`:

```c
U3V_TRIGGER_ACTIVATION_RISING_EDGE     U3V_TRIGGER_ACTIVATION_FALLING_EDGE
U3V_TRIGGER_SOURCE_LINE0 … LINE3        U3V_TRIGGER_SOURCE_SOFTWARE
U3V_TRIGGER_SELECTOR_ACQUISITION_START  U3V_TRIGGER_SELECTOR_FRAME_START
U3V_EXPOSURE_AUTO_OFF / _ONCE / _CONTINUOUS
```

### Faster, more stable color processing

The color ISP RGB filter uses significantly less CPU, improving the
stability of color live view on ARM hosts. Output is unchanged.

For smooth full-resolution color ISP (demosaic + RGB filter), an NVIDIA
Jetson Orin Nano or an x86_64 host is recommended. Lower-power boards
such as the Raspberry Pi 5 can run color but may not sustain smooth
full-resolution preview with the RGB filter enabled — see
`DELIVERY_OVERVIEW.md` §7.5.

## Compatibility

**Drop-in binary replacement — no relink required for existing code.**

- C/C++: replace `libu3v_cam.so.2.2.3` with `libu3v_cam.so.2.2.5`
  (SONAME `libu3v_cam.so.2` unchanged). On Windows, replace
  `u3v_cam.dll`. On macOS, replace `libu3v_cam.dylib`.
- Python: replace the `u3v-sdk-2.2.5-python.zip` package.

All existing 2.1.x / 2.2.x application code continues to work unchanged.

**To use the new functions and constants above**, update the SDK headers
together with the library and recompile your application — the headers and
library are a matched pair.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.2.5-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.2.5-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.2.5-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.2.5-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.2.5-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.2.5-python.zip` |

For installation and usage instructions, see the package README or
contact the SDK provider. For a summary of improvements across earlier
versions, see `WHATS_NEW.md`.
