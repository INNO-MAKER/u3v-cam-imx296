# U3V Camera SDK — Delivery Overview (Windows)

USB3 Vision (U3V) compliant camera SDK for Windows, with a GUI viewer,
reference examples, a Python interface, and an optional
image-signal-processing (ISP) plugin framework for color sensors.

- **SDK version:** 2.3.2
- **Platform:** Windows 10 / 11 x64
- **Color cameras:** supported through a pluggable ISP chain (demosaic,
  white balance, gamma, color correction). See §5.

---

## 1. What's in the Box

| File | Size | Purpose |
|---|---|---|
| `u3v-sdk-2.3.2-windows-x64.zip`        | 24 MB  | C/C++ SDK + Qt6 viewer, fully self-contained |
| `u3v-sdk-2.3.2-python-windows-x64.zip` | 0.2 MB | Python 3.8+ package with the bundled Windows native library |

Both packages share the same underlying SDK library. Code written against
the C API and code written against the Python `u3v_cam` package drive the
camera identically.

**File naming convention:**

```
u3v-sdk-<version>-<os>-<arch>.<ext>
```

The Python archive extracts to a top-level folder with the same base name
(`u3v-sdk-2.3.2-python-windows-x64/`). Extract the C/C++ archive into a
folder of its own, for example `u3v-sdk-2.3.2-windows-x64\`.

---

## 2. Supported Sensors

The SDK is a single shared codebase supporting multiple U3V camera
sensors. The C / Python API is identical across sensors — only the
sensor-specific values differ.

For sensor-specific parameters (resolution, pixel size, shutter type,
frame rate limits, supported pixel formats, gain range, and hardware
trigger capabilities), refer to the corresponding per-camera product
repository under the InnoMaker GitHub organization.

The package layout, install steps, and public API are shared and **do
not** change per sensor.

---

## 3. C/C++ Package (`u3v-sdk-2.3.2-windows-x64.zip`)

### 3.1 Target Audience

- **Windows 10 / 11 x64** users
- No prerequisites required — Qt6, Visual Studio runtime, libusb, and
  the USB driver installer are all bundled
- Truly out-of-the-box

### 3.2 Folder Layout

```
u3v-sdk-2.3.2-windows-x64/
├── README.txt                    Quick-start pointer
├── WINUSB_DRIVER_INSTALL.md      First-run driver install guide
├── bin/                          All runtime files (self-contained)
│   ├── u3v_viewer.exe            GUI viewer
│   ├── u3v_cam.dll               SDK library
│   ├── libusb-1.0.dll            USB transport
│   ├── Qt6Core.dll, Qt6Gui.dll,
│   │   Qt6Widgets.dll, ...       Qt6 framework + platform plugins
│   └── plugins/                  Optional ISP plugins (see §5)
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
1. Extract u3v-sdk-2.3.2-windows-x64.zip to any directory
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

MSVC command line:

```bat
cl my_app.c /I "<extracted>\include" "<extracted>\lib\u3v_cam.lib"
```

---

## 4. Python Package (`u3v-sdk-2.3.2-python-windows-x64.zip`)

### 4.1 Target Audience

- **Python 3.8+** users on Windows 10 / 11 x64
- ML / computer-vision / data-science workflows that need NumPy access
- Rapid prototyping, headless capture scripts, Jupyter notebooks
- Developers who want a PyQt6 viewer they can copy and modify

This package wraps the **same** native library the C/C++ package uses;
the Windows library is bundled inside, so no separate install is needed.

### 4.2 Folder Layout

```
u3v-sdk-2.3.2-python-windows-x64/
├── README.md                       Detailed Python usage guide
├── pyproject.toml                  PEP 517 metadata (`pip install .` works)
├── install_deps.bat                One-click dependency installer
├── run_basic_capture.bat           Capture 5 frames, save first as PGM
├── run_live_capture.bat            Continuous capture with FPS / drop stats
├── run_viewer.bat                  Launch the PyQt6 viewer
├── u3v_cam/                        Python package
│   ├── __init__.py                 High-level `Camera` class
│   ├── _raw.py                     1:1 ctypes binding
│   ├── _loader.py                  Native-library locator
│   └── _libs/windows-x64/          Bundled u3v_cam.dll + libusb + ISP plugins
├── examples/                       Capture, color, trigger, burst, viewer,
│                                   and frame-loss test scripts
└── tests/                          No-hardware load + symbol-resolution tests
```

### 4.3 Quick Start (End User)

```bat
:: 1. Extract u3v-sdk-2.3.2-python-windows-x64.zip
:: 2. Plug in the U3V camera (install the WinUSB driver if needed, see §3.3)
:: 3. Open a command prompt in the extracted folder
install_deps.bat
run_basic_capture.bat
run_viewer.bat
```

### 4.4 Application Development

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

Software trigger:
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

### 4.5 Optional Dependencies

| Feature | Package | Install |
|---|---|---|
| Core (always required) | `numpy>=1.20` | `pip install numpy` |
| `viewer_pyqt.py` | `PyQt6` + `pyqtgraph` | `pip install PyQt6 pyqtgraph` |
| `live_capture.py --show` | `opencv-python` | `pip install opencv-python` |

Or use `install_deps.bat` for an interactive prompt.

---

## 5. Color Support and the ISP Plugin Chain

### 5.1 What It Is

Color-variant sensors output Bayer-pattern frames that need demosaic
+ optional white balance / gamma / color correction before they look
right on a display or feed into downstream vision code.

The SDK ships three optional ISP plugins that handle this end-to-end:

- **`u3v_isp_color`** — Bayer demosaic + per-channel gain + offset +
  one-click auto white balance + optional histogram stretch
- **`u3v_gamma`** — display gamma correction (default off)
- **`u3v_ccm`** — 3×3 color correction matrix (default off, identity)

The plugins are `.dll` files that live under `bin\plugins\` next to the
viewer. The GUI viewer auto-loads whatever plugins are present at
startup; if the folder is empty, no ISP is applied and images pass
through unchanged.

### 5.2 GUI Controls

Open the viewer and expand the **Color / ISP** section in the parameter
panel. Each loaded plugin appears as its own group with type-safe
controls (sliders, drop-downs, checkboxes). Adjustments are live — no
stream restart needed. Click **White Balance** to auto-compute R/G/B
gains from the current frame; the values populate the sliders so you
can fine-tune from that baseline.

### 5.3 Programmatic Use

The pipeline is exposed to application code through two APIs:

```c
u3v_pipeline_t *pipe;
u3v_pipeline_create(&pipe);
u3v_pipeline_load_dir(pipe, "path\\to\\plugins");
u3v_stream_set_pipeline(stream, pipe);
/* subsequent u3v_stream_grab_view() returns processed frames */
```

In Python, the same demosaic pipeline is available through `enable_color()`:
```python
cam.pixel_format = u3v_cam.PFNC_BAYERRG8
cam.enable_color()                 # loads the bundled ISP plugins
rgb = cam.read_frame()             # (H, W, 3) uint8 RGB
```

Zero plugins loaded = pass-through; frames are delivered byte-for-byte
as the camera sent them.

### 5.4 Mono Sensors

Mono cameras pass through the ISP chain untouched — each plugin
recognises non-Bayer input and forwards frames unchanged, so a mono
workflow needs no changes to coexist with the color plugins.

### 5.5 Recommended Host for Color

Real-time color ISP (demosaic + RGB filter) is CPU-intensive. For smooth
full-resolution color live view with ISP filtering enabled, use a
desktop or mini-PC class x64 host. On lower-power machines, use the
simple demosaic, reduce the region of interest, or capture raw and
convert to color afterwards (see the Python README, **Burst capture**).
Mono capture runs comfortably on any supported host.

---

## 6. Quick Reference

| Aspect | C/C++ package | Python package |
|---|---|---|
| Package | `windows-x64.zip` | `python-windows-x64.zip` |
| Main library | `bin\u3v_cam.dll` | Bundled in `u3v_cam\_libs\windows-x64\` |
| Link / import | `lib\u3v_cam.lib` | `import u3v_cam` |
| GUI viewer | `bin\u3v_viewer.exe` | `examples\viewer_pyqt.py` |
| CLI sample | `examples\basic_capture.exe` | `examples\basic_capture.py` |
| ISP plugins | `bin\plugins\*.dll` | `u3v_cam\_libs\windows-x64\plugins\` |
| Qt6 runtime | Bundled | Optional pip `PyQt6` |
| USB library | Bundled `libusb-1.0.dll` | Bundled `libusb-1.0.dll` |
| Driver setup | WinUSB (Zadig) | WinUSB (Zadig) |

---

## 7. Public API Highlights

### C/C++

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

/* ISP pipeline (optional) */
u3v_status_t u3v_pipeline_create(u3v_pipeline_t **pipe);
u3v_status_t u3v_pipeline_load_dir(u3v_pipeline_t *pipe, const char *dir);
u3v_status_t u3v_stream_set_pipeline(u3v_stream_t *stream, u3v_pipeline_t *pipe);
u3v_status_t u3v_stream_set_auto_restart(u3v_stream_t *stream, int enable);
int          u3v_stream_get_auto_restart(const u3v_stream_t *stream);
uint32_t     u3v_stream_get_restart_count(const u3v_stream_t *stream);
```

For the full API surface, see the headers under `include\u3v\`.

### Python

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

## 8. Compatibility Matrix

| Item | Value |
|---|---|
| SDK version | 2.3.2 |
| USB protocol | USB3 Vision (U3V) v1.x compliant |
| Windows requirement | Windows 10 build 1809 or newer; all Windows 11 (x64) |
| Qt | 6.8.3 bundled |
| libusb | 1.0.x bundled |
| Recommended C/C++ compiler | MSVC 2022 |
| Python version | 3.8 – 3.12, CPython (64-bit) |
| Required Python packages | `numpy>=1.20` |
| Optional Python packages | `PyQt6 + pyqtgraph` (viewer), `opencv-python` (live preview) |
| Application binary compatibility | 2.1.x / 2.2.x code links and runs against 2.3.2 without recompile — replace `u3v_cam.dll` |

---

## 9. Delivery Notes

1. **Cloud share or email** — send the archives directly.
2. **USB stick or air-gapped sites** — copy the archives; no internet
   connection is needed to install.
3. **Choosing a package**
   - Desktop applications, system integrators →
     `u3v-sdk-2.3.2-windows-x64.zip`
   - Python / ML / CV teams, headless capture, scripting →
     `u3v-sdk-2.3.2-python-windows-x64.zip`
   - Color-camera users → the same package as above; the ISP plugins
     are included in both
   - Packages are **non-exclusive** — same underlying library, different
     interfaces. A customer can deploy both.

For technical support, contact the SDK provider.
