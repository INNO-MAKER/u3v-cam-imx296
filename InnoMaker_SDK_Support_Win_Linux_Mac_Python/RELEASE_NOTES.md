# U3V Camera SDK — Release Notes

**Version:** 2.2.6
**Date:** 2026-08-11
**Type:** Maintenance release (ABI-compatible)

---

## What's New

### Reliable frame handling

The SDK now signals incomplete frames with a distinct status,
`U3V_ERR_INCOMPLETE`, returned by `u3v_stream_grab` / `u3v_stream_grab_view`
when a frame did not arrive in full. Your application can check for it and
skip partial frames, keeping only complete images:

```c
u3v_status_t st = u3v_stream_grab(stream, &buf);
if (st == U3V_OK) {
    /* complete frame — use buf */
} else if (st == U3V_ERR_INCOMPLETE) {
    /* partial frame — skip it */
}
```

This is especially useful under heavy throughput, in hardware-trigger mode,
and in multi-camera setups. In Python, `read_frame()` raises
`U3VIncompleteFrame` (a subclass of `U3VError`), and iterating a camera
(`for frame in cam`) skips incomplete frames automatically.

### Color capture from the Python interface

The Python interface can return demosaiced color images directly. Enable the
color pipeline and `read_frame()` returns an `(H, W, 3)` RGB array — the same
color output as the viewer:

```python
cam.pixel_format = u3v_cam.PFNC_BAYERRG8
cam.enable_color()
rgb = cam.read_frame()      # (H, W, 3) uint8 RGB
```

`disable_color()` reverts to raw capture; `color_enabled` reports the current
state. See the Python package README for details.

## Compatibility

**Drop-in binary replacement — no relink required for existing code.**

- C/C++: replace `libu3v_cam.so.2.2.5` with `libu3v_cam.so.2.2.6`
  (SONAME `libu3v_cam.so.2` unchanged). On Windows, replace `u3v_cam.dll`.
  On macOS, replace `libu3v_cam.dylib`.
- Python: replace the `u3v-sdk-2.2.6.1-python.zip` package.

All existing 2.1.x / 2.2.x application code continues to work unchanged. Code
that checks grab results for `U3V_OK` treats an incomplete frame as a non-OK
result and can simply skip it.

**To use the new status constant or the Python color methods**, update the
SDK headers together with the library (C/C++), or use the new Python package.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.2.6-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.2.6-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.2.6-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.2.6-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.2.6-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.2.6.1-python.zip` |

For installation and usage instructions, see the package README or contact
the SDK provider. For a summary of improvements across earlier versions, see
`WHATS_NEW.md`.
