# U3V Camera SDK — Release Notes

**Version:** 2.3.2
**Date:** 2026-08-19
**Type:** Viewer feature release (ABI-compatible, drop-in library)

---

## What's New

### Accurate color on every color camera

The viewer's color live preview and white balance now render correct color
across the full range of supported color sensors and Bayer layouts. Open a
color camera and the preview shows true color; **Auto** white balance produces
a correctly balanced image.

### Expanded Color / ISP controls

The color camera's **Color / ISP** panel now offers:

- **Bayer Pattern** — Auto (follows the camera) plus manual RGGB / GRBG / GBRG /
  BGGR selection.
- **White Balance** — red, green, and blue gain, or press **Auto** for a
  one-shot automatic white balance.
- **Gamma** — adjustable gamma curve.
- **Color Matrix** — a 3×3 color-correction matrix.
- **Histogram** — dark / light levels with a one-shot **Auto**.

All controls are off by default and apply only to color cameras; mono cameras
are unaffected.

### Steadier, tunable preview

A selectable preview display rate (**Preview → Display FPS**, default 25 fps)
keeps the live view smooth on any host. This is a display setting only —
image capture always runs at the full sensor frame rate.

## Compatibility

**Drop-in binary replacement — no API or ABI change, no relink required.**

This release improves the viewer; the library API is identical to 2.3.1.

- C/C++: replace `u3v_cam.dll` (Windows), `libu3v_cam.so.2.3.x` (Linux), or
  `libu3v_cam.dylib` (macOS). SONAME `libu3v_cam.so.2` unchanged.
- Python: replace the `u3v-sdk-2.3.2-python.zip` package.

All existing 2.1.x / 2.2.x / 2.3.x application code continues to work unchanged.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.3.2-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.3.2-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.3.2-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.3.2-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.3.2-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.3.2-python.zip` |

For installation and usage instructions, see the package README or contact
the SDK provider. For a summary of improvements across earlier versions, see
`WHATS_NEW.md`.
