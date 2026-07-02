# U3V Camera SDK — Release Notes

**Version:** 2.2.0
**Date:** 2026-07-02
**Type:** Feature release (backward compatible)

---

## What's New

### Color camera support

Optional image-signal-processing (ISP) plugin chain applied to
Bayer-pattern sensors before frames reach your application.

Three official plugins ship in `bin/plugins/`:

- **`u3v_isp_color`** — Bayer demosaic, per-channel gain and offset,
  one-click auto white balance, optional histogram stretch.
- **`u3v_gamma`** — Display gamma correction (off by default).
- **`u3v_ccm`** — 3×3 color correction matrix (off by default,
  identity).

Empty `plugins/` folder = no ISP applied, images pass through
unchanged.

Mono sensors are unaffected — frames pass through the chain
untouched. Mono workflows continue to work without any modification.

### GUI viewer

- New **Color / ISP** section in the parameter panel with live
  controls for every loaded plugin. Adjustments take effect without
  restarting the stream.
- **White Balance** button auto-computes R/G/B gains from the current
  frame and populates the sliders so you can fine-tune from the auto
  baseline.
- New **Reset Framerate** button — one-click recovery for the rare
  case where frame rate stays capped after a large exposure change.
- Exposure and gain adjustments apply live; frame rate changes still
  restart internally.

### SDK API additions

New public headers and functions for the ISP framework. Strictly
additive — existing application code compiles and links against
2.2.0 without any source changes.

```c
u3v_pipeline_create(...)
u3v_pipeline_load_dir(...)
u3v_stream_set_pipeline(...)
u3v_stream_grab_view(...)
```

Plus a plugin ABI in `<u3v/u3v_plugin.h>` for authoring third-party
ISP plugins.

---

## Compatibility

- Existing C/C++ application code compiles and links against 2.2.0
  with no source changes.
- Existing Python application code continues to work with the 2.2.0
  Python package.
- Mono workflows are unchanged.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.2.0-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.2.0-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.2.0-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.2.0-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.2.0-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.2.0-python.zip` |

For installation and usage instructions, see the package README or
contact the SDK provider.
