# U3V Camera SDK — Library Usage Guide

**Version:** 2.3.2

This guide covers how to integrate and deploy the U3V Camera library
(`u3v_cam.dll` / `libu3v_cam.so` / `libu3v_cam.dylib`) in your own
application. If you only need the Python interface, jump to
[Python](#python-38).

---

## 1. Where the library lives in each package

| Package | Library file | Import library / header |
|---|---|---|
| `u3v-sdk-2.3.2-windows-x64.zip` | `bin/u3v_cam.dll` (+ `bin/libusb-1.0.dll`) | `lib/u3v_cam.lib`, `include/u3v/*.h` |
| `u3v-sdk-2.3.2-linux-x64.tar.gz` | `lib/libu3v_cam.so.2.3.2` (+ SONAME symlinks) | `include/u3v/*.h` |
| `u3v-sdk-2.3.2-linux-arm64.tar.gz` | Same layout as x64 | Same |
| `u3v-sdk-2.3.2-macos-arm64.zip` | `lib/libu3v_cam.2.3.2.dylib` (+ symlinks) | `include/u3v/*.h` |
| `u3v-sdk-2.3.2-macos-x64.zip` | Same layout as arm64 | Same |
| `u3v-sdk-2.3.2-python.zip` | Native libraries for all five platforms bundled inside | Not required — Python wraps them |

Optional ISP color plugins for demosaic / white-balance / gamma / CCM
ship under `bin/plugins/` on every platform.

---

## 2. C / C++ integration

### Include and link

```c
#include <u3v/u3v_sdk.h>
```

| Platform | Link flag |
|---|---|
| Windows (MSVC) | `u3v_cam.lib` |
| Linux (GCC / Clang) | `-lu3v_cam` |
| macOS (Clang) | `-lu3v_cam` |

Minimum runtime dependency: **libusb 1.0**.
- Windows: ship `libusb-1.0.dll` next to your application executable.
- Linux: install `libusb-1.0-0` via your distro package manager.
- macOS: install via Homebrew (`brew install libusb`) or bundle it
  inside your app.

### Minimum example

```c
#include <u3v/u3v_sdk.h>

int main(void) {
    u3v_sdk_init();

    u3v_camera_t *cam = NULL;
    u3v_camera_open(&cam, 0);

    u3v_camera_set_pixel_format(cam, PFNC_MONO8);
    u3v_camera_set_exposure(cam, 10000);   /* 10 ms */
    u3v_camera_set_acq_mode(cam, U3V_ACQ_MODE_CONTINUOUS);

    u3v_stream_t *stream = NULL;
    u3v_stream_config_t cfg = { .num_buffers = 4, .timeout_ms = 5000 };
    u3v_stream_create(&stream, cam, &cfg);

    u3v_buffer_t buf;
    uint32_t payload; u3v_camera_get_payload_size(cam, &payload);
    u3v_buffer_alloc(&buf, payload);

    u3v_camera_start(cam);
    u3v_stream_grab(stream, &buf);
    u3v_camera_stop(cam);

    u3v_buffer_free(&buf);
    u3v_stream_destroy(stream);
    u3v_camera_close(cam);
    u3v_sdk_shutdown();
    return 0;
}
```

The full working versions are `examples/basic_capture.c` (continuous
capture) and `examples/multi_frame_capture.c` (fixed-count capture).

---

## 3. Runtime deployment

When shipping your compiled application to a target machine, copy the
following files. You do **not** need the SDK's `include/`, `lib/`, or
`examples/` at runtime.

### Windows
Copy alongside your `.exe`:
```
u3v_cam.dll
libusb-1.0.dll
plugins/            (optional, only if you use the ISP plugins)
```

### Linux (x64 or ARM64)
Install to your app's rpath or a system location such as `/usr/local/lib/`:
```
libu3v_cam.so.2.3.2
libu3v_cam.so.2          (symlink to libu3v_cam.so.2.3.2)
plugins/                 (optional)
```
Run `sudo ldconfig` after installing to `/usr/local/lib/`.
libusb-1.0 must be provided by the distro (`libusb-1.0-0`).

### macOS
Copy inside your `.app` bundle's `Contents/Frameworks/` or install to
`/usr/local/lib/`:
```
libu3v_cam.2.3.2.dylib
libu3v_cam.2.dylib       (symlink)
libu3v_cam.dylib         (symlink)
plugins/                 (optional)
```
libusb via Homebrew or embedded in your bundle.

---

## 4. Overriding where the library is loaded from

At runtime, the library search order is:

1. Environment variable override (if set)
2. Same directory as the executable (Windows) or standard system paths
   (Linux/macOS)
3. For Python: the bundled `_libs/<platform>/` inside the installed
   package

To force a specific build:

| Platform | Environment variable |
|---|---|
| Windows | `U3V_CAM_DLL=C:\path\to\u3v_cam.dll` |
| Linux / macOS | `U3V_CAM_LIB=/path/to/libu3v_cam.so.2` |

---

## 5. Python (3.8+)

The Python package bundles the native library for every supported
platform. No separate C library install is required.

```bash
pip install u3v-sdk-2.3.2-python.zip
```

```python
import u3v_cam

with u3v_cam.Camera() as cam:
    cam.exposure_us = 10000
    cam.gain = 0
    cam.start()
    for _ in range(60):
        frame = cam.read_frame()   # numpy.ndarray (H, W)
    cam.stop()
```

MultiFrame capture:
```python
cam.set_acq_mode(u3v_cam.ACQ_MODE_MULTI_FRAME)
cam.frame_count = 5
cam.start()
for _ in range(5):
    frame = cam.read_frame()
cam.stop()
```

For live viewing and more examples, install with the viewer extras:
```bash
pip install "u3v_cam[viewer]"    # PyQt6 + pyqtgraph
```

---

## 6. Binary compatibility

The library preserves ABI within the 2.x line — SONAME
`libu3v_cam.so.2` (Linux) / current version `2` (macOS) does not change
across minor releases. Application code linked against 2.1.x or 2.2.x
runs against 2.3.2 without recompile; drop the new library file in
place of the old one.

---

## 7. Where to look next

- `RELEASE_NOTES.md` — current version's new features, fixes, and
  supported platforms.
- `WHATS_NEW.md` — summary of improvements across versions.
- `DELIVERY_OVERVIEW.md` — full package inventory and per-platform
  installation walkthrough.
- `TRIGGER_USAGE.md` — external hardware trigger guide (pulse width,
  wiring, Arduino UNO code, SDK trigger configuration).
- Each package's `docs/` — inline release notes matching the package
  version.
