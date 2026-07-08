# U3V Camera SDK — Release Notes

**Version:** 2.2.2
**Date:** 2026-07-08
**Type:** Feature + bug fix release (ABI-compatible)

---

## What's New

### MultiFrame acquisition
Added `AcquisitionFrameCount` API and the `multi_frame_capture` example.
Set `AcquisitionMode` to `MultiFrame` and `AcquisitionFrameCount` to N;
the camera captures exactly N frames and stops automatically.

C:
```c
u3v_camera_set_acq_mode(cam, U3V_ACQ_MODE_MULTI_FRAME);
u3v_camera_set_frame_count(cam, 5);
u3v_camera_start(cam);
```

Python:
```python
cam.set_acq_mode(u3v_cam.ACQ_MODE_MULTI_FRAME)
cam.frame_count = 5
cam.start()
```

## What's Fixed

### `Camera.info` fields
Manufacturer, model, and serial number strings now return their full
values correctly on all supported camera firmware.

### Frame rate control
`cam.frame_rate = N` / `u3v_camera_set_frame_rate()` now takes effect
reliably whether called before or during streaming.
`u3v_camera_get_frame_rate()` / reading `cam.frame_rate` returns the
actual configured FPS.

### Console output
SDK initialization no longer emits diagnostic messages to stderr in
normal operation. The SDK's behavior itself is unchanged — only the
console noise is quieted.

---

## Compatibility

**Drop-in binary replacement — no source changes, no relink required.**

- C/C++: replace `libu3v_cam.so.2.2.1` with `libu3v_cam.so.2.2.2`
  (SONAME `libu3v_cam.so.2` unchanged). On Windows, replace
  `u3v_cam.dll`. On macOS, replace `libu3v_cam.dylib`.
- Python: replace the `u3v-sdk-2.2.2-python.zip` package.

All existing 2.1.x / 2.2.x application code continues to work unchanged.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.2.2-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.2.2-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.2.2-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.2.2-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.2.2-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.2.2-python.zip` |

For installation and usage instructions, see the package README or
contact the SDK provider.
