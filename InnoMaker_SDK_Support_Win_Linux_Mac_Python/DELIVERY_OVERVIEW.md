# U3V Camera SDK — Delivery Overview

USB3 Vision (U3V) compliant camera SDK with cross-platform binaries,
GUI viewer, reference examples, and an optional image-signal-processing
(ISP) plugin framework for color sensors.

- **SDK version:** 2.3.0
- **Last updated:** 2026-08-15
- **What's new in 2.2.0:** color-camera support via a pluggable ISP
  chain (demosaic, white balance, gamma, color correction). See §7.

---

## 1. What's in the Box

Six packages, each self-describing by filename. Pick the one for your
target platform.

| File | Platform | Size | Purpose |
|---|---|---|---|
| `u3v-sdk-2.3.0-windows-x64.zip`   | Windows 10/11 x64 | 18 MB | C/C++ SDK + Qt6 viewer, fully self-contained |
| `u3v-sdk-2.3.0-linux-x64.tar.gz`  | Ubuntu 22.04+ x64 | 150 KB | C/C++ SDK + viewer (system Qt6 + libusb) |
| `u3v-sdk-2.3.0-linux-arm64.tar.gz`| Pi 5 / Jetson Orin Nano | 150 KB | ARM64 counterpart of the Linux x64 package |
| `u3v-sdk-2.3.0-macos-arm64.zip`   | Apple Silicon (M1–M4) | 55 MB | C/C++ SDK + `u3v_viewer.app` |
| `u3v-sdk-2.3.0-macos-x64.zip`     | Intel Mac | 55 MB | Intel counterpart of the macOS arm64 package |
| `u3v-sdk-2.3.0-python.zip`        | All five OS/arch above | 1.1 MB | Python 3.8+ package; bundles native libraries for every platform in one archive |

All packages share the same underlying SDK. Code written against the C
API on Windows compiles and runs unchanged on Linux and macOS. Python
code written against `u3v_cam` runs unchanged on Windows, Linux, and
macOS.

**File naming convention** (locked from 2.2.0 onward):

```
u3v-sdk-<version>-<os>-<arch>.<ext>
```

The archive extracts to a top-level folder with the same base name
(e.g. `u3v-sdk-2.3.0-windows-x64/`), so multiple platforms can coexist
in one directory without collisions.

---

## 2. Supported Sensors

The SDK is a single shared codebase supporting multiple U3V camera
sensors through a runtime dispatch table. The C / Python API is
identical across sensors — only the sensor-specific values differ.

For sensor-specific parameters (resolution, pixel size, shutter type,
frame rate limits, supported pixel formats, gain range, hardware
trigger capabilities, and verified host platforms), refer to the
corresponding per-camera product repository under the InnoMaker GitHub
organization.

The package layout, install steps, and public API are shared and **do
not** change per sensor.

---

## 3. Windows Package (`u3v-sdk-2.3.0-windows-x64.zip`)

### 3.1 Target Audience

- **Windows 10 / 11 x64** users
- No prerequisites required — Qt6, Visual Studio runtime, libusb, and
  the USB driver installer are all bundled
- Truly out-of-the-box

### 3.2 Folder Layout

```
u3v-sdk-2.3.0-windows-x64/
├── README.txt                    Quick-start pointer
├── WINUSB_DRIVER_INSTALL.md      First-run driver install guide
├── bin/                          All runtime files (~18 MB, self-contained)
│   ├── u3v_viewer.exe            GUI viewer
│   ├── u3v_cam.dll               SDK library
│   ├── libusb-1.0.dll            USB transport
│   ├── Qt6Core.dll, Qt6Gui.dll,
│   │   Qt6Widgets.dll, ...       Qt6 framework + platform plugins
│   └── plugins/                  Optional ISP plugins (see §7)
│       ├── u3v_isp_color.dll
│       ├── u3v_gamma.dll
│       └── u3v_ccm.dll
├── lib/u3v_cam.lib               Import library — link your application
├── include/u3v/*.h               Public API headers (7 files)
├── examples/
│   ├── basic_capture.exe         CLI capture demo
│   ├── basic_capture.c           Compilable reference source
│   ├── multi_frame_capture.exe   MultiFrame N-frame acquisition demo
│   └── multi_frame_capture.c     Compilable reference source
├── tools/                        WinUSB driver installer
│   ├── zadig-2.9.exe             Zadig 2.9 (GPLv3, redistributed)
│   ├── LICENSE-zadig.txt         GPLv3 notice for the Zadig binary
│   └── README-driver.txt         Quick reference for driver install
└── docs/
    └── RELEASE_NOTES.md          Release notes for this version
```

### 3.3 Quick Start (End User)

```
1. Extract u3v-sdk-2.3.0-windows-x64.zip to any directory
2. Plug in the U3V camera
3. First-time on this machine: install the WinUSB driver
   - Read WINUSB_DRIVER_INSTALL.md (at the package root)
   - Run tools\zadig-2.9.exe as Administrator, follow the guide
4. Double-click bin\u3v_viewer.exe
   → GUI launches; your camera appears in the device list
```

### 3.4 Application Development

Linking the SDK into your own program:

```cpp
#include <u3v/u3v_sdk.h>

// MSVC project settings:
//   Additional Include Directories:  <extracted>\include
//   Additional Library Directories:  <extracted>\lib
//   Additional Dependencies:         u3v_cam.lib
// At runtime, ensure <extracted>\bin is on PATH, or copy the contents
// of bin\ next to your executable.
```

---

## 4. Linux Package (`u3v-sdk-2.3.0-linux-x64.tar.gz` / `-linux-arm64.tar.gz`)

### 4.1 Target Audience

- **Ubuntu 22.04 LTS or newer** (Debian 12+, Raspberry Pi OS Bookworm)
- **x86_64** on PCs and **ARM64** on Raspberry Pi 5 / NVIDIA Jetson
- System packages required: `libusb-1.0-0`, `libqt6widgets6` (one apt command)

Both packages share the same folder layout — only the architecture of
the compiled binaries differs.

### 4.2 Folder Layout

```
u3v-sdk-2.3.0-linux-<arch>/
├── bin/
│   ├── u3v_viewer                GUI viewer
│   ├── basic_capture             CLI capture demo
│   └── plugins/                  Optional ISP plugins (see §7)
│       ├── u3v_isp_color.so
│       ├── u3v_gamma.so
│       └── u3v_ccm.so
├── lib/
│   ├── libu3v_cam.so             → libu3v_cam.so.2
│   ├── libu3v_cam.so.2           → libu3v_cam.so.2.3.0
│   └── libu3v_cam.so.2.3.0       Actual library
├── include/u3v/*.h               Public API headers (7 files)
├── examples/
│   └── basic_capture.c           Compilable reference source
├── docs/
│   └── CHANGELOG.md              Release notes
└── run_viewer.sh                 Launch script (sets LD_LIBRARY_PATH)
```

### 4.3 Quick Start (End User)

```bash
# 1. Extract for your architecture
tar xzf u3v-sdk-2.3.0-linux-x64.tar.gz          # x86_64 PC / Docker
# or
tar xzf u3v-sdk-2.3.0-linux-arm64.tar.gz        # Pi 5 / Jetson
cd u3v-sdk-2.3.0-linux-*

# 2. Install runtime dependencies (one-time)
sudo apt update
sudo apt install -y libusb-1.0-0 libqt6widgets6

# 3. Install a udev rule so non-root users can open U3V cameras
sudo tee /etc/udev/rules.d/99-u3v.rules > /dev/null <<'EOF'
SUBSYSTEM=="usb", ATTRS{bDeviceClass}=="ef", ATTRS{bDeviceSubClass}=="02", ATTRS{bDeviceProtocol}=="01", MODE="0666", GROUP="plugdev"
EOF
sudo udevadm control --reload-rules && sudo udevadm trigger
sudo usermod -aG plugdev $USER
# Log out and back in for the group change to take effect

# 4. Launch the viewer
./run_viewer.sh
```

### 4.4 Application Development

```bash
gcc my_app.c \
    -I ./include \
    -L ./lib -lu3v_cam \
    -Wl,-rpath,'$ORIGIN/lib' \
    -o my_app
```

The `-Wl,-rpath,'$ORIGIN/lib'` flag lets `my_app` find `libu3v_cam.so`
next to itself at runtime, so you can ship the binary together with the
`lib/` folder.

---

## 5. macOS Packages (`u3v-sdk-2.3.0-macos-arm64.zip` / `-macos-x64.zip`)

### 5.1 Target Audience

- **macOS 11 (Big Sur) or newer**
- Apple Silicon (M1 / M2 / M3 / M4) **and** Intel Macs — separate
  archive per architecture, pick the matching one
- Prerequisites: Homebrew + `brew install libusb` (one command)

Why per-architecture instead of a universal binary: Homebrew distributes
single-architecture bottles, so a fat Mach-O is impractical to build on
a single host. Building on the matching Mac keeps the toolchain simple.

### 5.2 Folder Layout

```
u3v-sdk-2.3.0-macos-<arch>/
├── bin/
│   ├── u3v_viewer.app            GUI viewer bundle (Qt6 embedded)
│   ├── basic_capture             CLI capture demo
│   └── plugins/                  Optional ISP plugins (see §7)
│       ├── u3v_isp_color.dylib
│       ├── u3v_gamma.dylib
│       └── u3v_ccm.dylib
├── lib/
│   ├── libu3v_cam.dylib          → libu3v_cam.2.dylib
│   ├── libu3v_cam.2.dylib        → libu3v_cam.2.3.0.dylib
│   └── libu3v_cam.2.3.0.dylib    Actual library
├── include/u3v/*.h               Public API headers (7 files)
├── examples/basic_capture.c      Compilable reference source
├── docs/CHANGELOG.md              Release notes
└── run_viewer.sh                 Launch script
```

### 5.3 Quick Start (End User)

```bash
# 1. Extract the matching archive
unzip u3v-sdk-2.3.0-macos-arm64.zip     # Apple Silicon
# or
unzip u3v-sdk-2.3.0-macos-x64.zip       # Intel

cd u3v-sdk-2.3.0-macos-*

# 2. Install libusb (one-time). Install Homebrew first if you don't
#    have it: https://brew.sh/
brew install libusb

# 3. Launch the viewer
open bin/u3v_viewer.app
```

If macOS Gatekeeper blocks the unsigned `.app` ("cannot be opened
because the developer cannot be verified"): right-click → **Open** →
**Open Anyway** (first time only; double-click works thereafter).

### 5.4 Application Development

```bash
clang my_app.c \
    -I ./include \
    -L ./lib -lu3v_cam \
    -Wl,-rpath,@loader_path/lib \
    -o my_app
```

The `@loader_path/lib` rpath lets `my_app` find `libu3v_cam.dylib`
next to itself at runtime — equivalent to the Linux `$ORIGIN/lib`
pattern.

---

## 6. Python Package (`u3v-sdk-2.3.0-python.zip`)

### 6.1 Target Audience

- **Python 3.8+** users on any of the five supported OS/arch combos
- ML / computer-vision / data-science workflows that need NumPy access
- Rapid prototyping, headless capture scripts, Jupyter notebooks
- Developers who want a PyQt6 viewer they can copy and modify

This package wraps the **same** native library the C/C++ packages use.
A single archive covers all five platforms — the loader picks the
matching binary at import time.

### 6.2 Folder Layout

```
u3v-sdk-2.3.0-python/
├── README.md                       Detailed Python usage guide
├── pyproject.toml                  PEP 517 metadata (`pip install .` works)
├── install_deps.bat / .sh          One-click dependency installer
├── run_basic_capture.bat / .sh     Capture 5 frames, save first as PGM
├── run_live_capture.bat / .sh      Continuous capture with FPS / drop stats
├── run_viewer.bat / .sh            Launch the PyQt6 viewer
├── u3v_cam/                        Python package
│   ├── __init__.py                 High-level `Camera` class
│   ├── _raw.py                     1:1 ctypes binding
│   ├── _loader.py                  Cross-platform native-library locator
│   └── _libs/                      Native libraries (auto-selected)
│       ├── windows-x64/
│       ├── linux-x64/
│       ├── linux-arm64/
│       ├── macos-arm64/
│       └── macos-x64/
├── examples/
│   ├── basic_capture.py
│   ├── live_capture.py
│   ├── trigger_mode.py
│   ├── roi_exposure.py
│   └── viewer_pyqt.py              Full PyQt6 viewer (~290 lines)
└── tests/test_smoke.py             No-hardware load + symbol-resolution test
```

### 6.3 Quick Start (End User)

**Windows:**
```bat
:: 1. Extract u3v-sdk-2.3.0-python.zip
:: 2. Plug in the U3V camera (install WinUSB driver if needed)
:: 3. Open a command prompt in the extracted folder
install_deps.bat
run_basic_capture.bat
run_viewer.bat
```

**Linux / macOS:**
```bash
unzip u3v-sdk-2.3.0-python.zip
cd u3v-sdk-2.3.0-python
chmod +x *.sh
./install_deps.sh
./run_basic_capture.sh
./run_viewer.sh
```

Linux prerequisites (same as §4.3): `libusb-1.0-0` + udev rule.
macOS prerequisites (same as §5.3): Homebrew + `brew install libusb`.

### 6.4 Application Development

```python
import u3v_cam

# Discover cameras
print(u3v_cam.list_cameras())
# -> [{'index': 0, 'manufacturer': '...', 'serial': '...', ...}]

# Open, configure, capture
with u3v_cam.Camera() as cam:
    cam.set_roi(1456, 1088)
    cam.pixel_format = u3v_cam.PFNC_MONO8
    cam.start()
    cam.exposure_us  = 5000
    cam.gain         = 0
    cam.frame_rate   = 60
    for _ in range(60):
        frame = cam.read_frame()        # numpy.ndarray (H, W) uint8
    cam.stop()
```

Hardware trigger:
```python
cam.configure_trigger(on=True, source="software")
cam.start()
cam.software_trigger()
frame = cam.read_frame()
```

Color capture (color sensors):
```python
cam.pixel_format = u3v_cam.PFNC_BAYERRG8   # 8-bit Bayer color
cam.enable_color()                         # demosaic to RGB via the ISP plugins
cam.start()
rgb = cam.read_frame()                     # numpy.ndarray (H, W, 3) uint8 RGB
```

### 6.5 Optional Dependencies

| Feature | Package | Install |
|---|---|---|
| Core (always required) | `numpy>=1.20` | `pip install numpy` |
| `viewer_pyqt.py` | `PyQt6` + `pyqtgraph` | `pip install PyQt6 pyqtgraph` |
| `live_capture.py --show` | `opencv-python` | `pip install opencv-python` |

Or use `install_deps.bat` / `install_deps.sh` for an interactive prompt.

---

## 7. Color Support and the ISP Plugin Chain (New in 2.2.0)

### 7.1 What It Is

Color-variant sensors output Bayer-pattern frames that need demosaic
+ optional white balance / gamma / color correction before they look
right on a display or feed into downstream vision code.

2.2.0 ships three optional ISP plugins that handle this end-to-end:

- **`u3v_isp_color`** — Bayer demosaic + per-channel gain + offset +
  one-click auto white balance + optional histogram stretch
- **`u3v_gamma`** — display gamma correction (default off)
- **`u3v_ccm`** — 3×3 color correction matrix (default off, identity)

The plugins are `.dll` / `.so` / `.dylib` files that live under
`bin/plugins/` next to the viewer. The GUI viewer auto-loads whatever
plugins are present at startup; if the folder is empty, no ISP is
applied and images pass through unchanged.

### 7.2 GUI Controls

Open the viewer and expand the **Color / ISP** section in the parameter
panel. Each loaded plugin appears as its own group with type-safe
controls (sliders, drop-downs, checkboxes). Adjustments are live — no
stream restart needed. Click **White Balance** to auto-compute R/G/B
gains from the current frame; the values populate the sliders so you
can fine-tune from that baseline.

### 7.3 Programmatic Use

The pipeline is exposed to application code through two APIs:

```c
u3v_pipeline_t *pipe;
u3v_pipeline_create(&pipe);
u3v_pipeline_load_dir(pipe, "path/to/plugins");
u3v_stream_set_pipeline(stream, pipe);
/* subsequent u3v_stream_grab_view() returns processed frames */
```

In Python, the same demosaic pipeline is available through `enable_color()`:
```python
cam.pixel_format = u3v_cam.PFNC_BAYERRG8
cam.enable_color()                 # loads the bundled ISP plugins
rgb = cam.read_frame()             # (H, W, 3) uint8 RGB
```

Zero plugins loaded = pass-through; behaviour is byte-for-byte identical
to 2.1.x.

### 7.4 Mono Sensors

Mono cameras (Mono8/10/12/16) pass through the ISP chain untouched —
each plugin recognises non-Bayer input and forwards frames unchanged.
Mono workflows written against 2.1.x continue to work unchanged.

### 7.5 Recommended Host for Color

Real-time color ISP (demosaic + RGB filter) is CPU-intensive. For smooth
full-resolution color live view with ISP filtering enabled, we recommend
an **NVIDIA Jetson Orin Nano** or an **x86_64 host** (PC / mini-PC).
Lower-power boards such as the **Raspberry Pi 5** can run color, but may
not sustain smooth full-resolution preview with the RGB filter enabled;
on such hosts, use the simple demosaic, reduce the region of interest, or
prefer a mono sensor. Mono capture runs comfortably on all supported
platforms.

---

## 8. Cross-Platform Quick Reference

| Aspect | Windows | Linux | macOS | Python |
|---|---|---|---|---|
| Package | `windows-x64.zip` | `linux-{x64,arm64}.tar.gz` | `macos-{arm64,x64}.zip` | `python.zip` |
| Main library | `bin\u3v_cam.dll` | `lib/libu3v_cam.so.*` | `lib/libu3v_cam.*.dylib` | Same, bundled in `_libs/<platform>/` |
| Link / import | `lib\u3v_cam.lib` | `-lu3v_cam` | `-lu3v_cam` | `import u3v_cam` |
| GUI viewer | `bin\u3v_viewer.exe` | `bin/u3v_viewer` | `bin/u3v_viewer.app` | `examples/viewer_pyqt.py` |
| CLI sample | `bin\basic_capture.exe` | `bin/basic_capture` | `bin/basic_capture` | `examples/basic_capture.py` |
| ISP plugins | `bin\plugins\*.dll` | `bin/plugins/*.so` | `bin/plugins/*.dylib` | `_libs/<platform>/plugins/` |
| Qt6 runtime | Bundled | System `libqt6widgets6` | Bundled (`.app`) | Optional pip `PyQt6` |
| USB library | Bundled `libusb-1.0.dll` | System `libusb-1.0-0` | Homebrew `libusb` | Same as native |
| Driver setup | WinUSB (Zadig) | udev rule (§4.3) | None | Same as matching native |
| Architectures | x64 | x64 + arm64 | arm64 + x64 | All 5 |

---

## 9. Source Compatibility Across Platforms

The `include/u3v/*.h` headers are **identical** in every native package.
Application code written on Windows recompiles on Linux or macOS
without changes. The Python package binds the same public functions
through `u3v_cam._raw`, so behaviour is consistent across all packages.

### Public API Highlights — C/C++

```c
/* Initialization */
u3v_status_t u3v_sdk_init(void);
void         u3v_sdk_shutdown(void);

/* Device discovery and connection */
int          u3v_discover(u3v_device_info_t *info, int max_devices);
u3v_status_t u3v_camera_open(u3v_camera_t **cam, int device_index);
void         u3v_camera_close(u3v_camera_t *cam);

/* Camera control */
u3v_status_t u3v_camera_get_temperature(u3v_camera_t *cam, uint32_t *temp);
u3v_status_t u3v_camera_set_exposure(u3v_camera_t *cam, uint32_t microseconds);
u3v_status_t u3v_camera_set_gain(u3v_camera_t *cam, uint32_t gain);
u3v_status_t u3v_camera_set_roi(u3v_camera_t *cam,
                                  uint32_t w, uint32_t h,
                                  uint32_t off_x, uint32_t off_y);
u3v_status_t u3v_camera_find_me(u3v_camera_t *cam);

/* Streaming */
u3v_status_t u3v_stream_create(u3v_stream_t **stream, u3v_camera_t *cam,
                                const u3v_stream_config_t *config);
u3v_status_t u3v_camera_start(u3v_camera_t *cam);
u3v_status_t u3v_stream_grab(u3v_stream_t *stream, u3v_buffer_t *buf);
u3v_status_t u3v_stream_grab_view(u3v_stream_t *stream, u3v_buffer_t *out);
u3v_status_t u3v_camera_stop(u3v_camera_t *cam);

/* ISP pipeline (optional, 2.2.0+) */
u3v_status_t u3v_pipeline_create(u3v_pipeline_t **pipe);
u3v_status_t u3v_pipeline_load_dir(u3v_pipeline_t *pipe, const char *dir);
u3v_status_t u3v_stream_set_pipeline(u3v_stream_t *stream, u3v_pipeline_t *pipe);
```

For the full API surface, see the headers under `include/u3v/`.

### Public API Highlights — Python

```python
import u3v_cam

# Discovery
u3v_cam.list_cameras()

# High-level Camera class
with u3v_cam.Camera() as cam:
    cam.exposure_us = 5000
    cam.gain        = 0
    cam.set_roi(1456, 1088)
    cam.start()
    frame = cam.read_frame()
    cam.stop()

# Low-level 1:1 binding (escape hatch)
from u3v_cam import _raw
_raw.camera_set_exposure(cam._handle, 5000)
```

---

## 10. Compatibility Matrix

| Item | Value |
|---|---|
| SDK version | 2.3.0 |
| USB protocol | USB3 Vision (U3V) v1.x compliant |
| Windows requirement | Windows 10 build 1809 or newer; all Windows 11 |
| Linux glibc requirement | 2.35 or newer (Ubuntu 22.04+) |
| macOS requirement | 11 (Big Sur) or newer |
| Qt | 6.8.3 bundled (Windows / macOS) / 6.2+ system (Linux) |
| libusb | 1.0.x |
| Recommended C/C++ compiler | MSVC 2022 (Windows) / GCC 11+ (Linux) / Xcode Clang 13+ (macOS) |
| Python version | 3.8 – 3.12, CPython |
| Required Python packages | `numpy>=1.20` |
| Optional Python packages | `PyQt6 + pyqtgraph` (viewer), `opencv-python` (live preview) |
| Application binary compatibility | 2.1.x / 2.2.x code links and runs against 2.3.0 without recompile (SONAME libu3v_cam.so.2 unchanged) |

---

## 11. Delivery Notes

1. **Cloud share or email** — send the archive directly. Windows,
   macOS, and Python packages compress well; Linux archives are small
   already.
2. **USB stick or air-gapped sites** — copy the archive.
3. **Custom enterprise deployment** — the package layout is suitable as
   a base for re-branding (logo, code-signing, license-key gating).
4. **Choosing a package**
   - Desktop applications, system integrators →
     `u3v-sdk-2.3.0-windows-x64.zip` and/or
     `u3v-sdk-2.3.0-linux-<arch>.tar.gz` and/or
     `u3v-sdk-2.3.0-macos-<arch>.zip`
   - Python / ML / CV teams, headless capture, scripting →
     `u3v-sdk-2.3.0-python.zip` (single file covers all five platforms)
   - Color-camera users on any platform → the same package as above
     plus the ISP plugins in `bin/plugins/`
   - Packages are **non-exclusive** — same underlying library, different
     interfaces. A customer can deploy any combination.

For technical support, contact the SDK provider.
