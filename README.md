# U3V-CAM-IMX296 USB3 Vision Industrial Camera

![U3V-CAM-IMX296](./images/InnoMaker_USB3_Vision_Industrial_Camera_IMX296_1.58MP_Monochrome_Global_Shutter_1456x1088@60FPS_GenICam_Compliant_Hardware_Trigger_Strobe_Arbitrary_ROI_01.jpg "U3V-CAM-IMX296")

The **U3V-CAM-IMX296** is a high-performance USB3 Vision industrial camera featuring the **Sony IMX296LLR** (monochrome) global shutter CMOS sensor. With a resolution of **1.58 MP (1456 × 1088)** and a full-resolution frame rate of **60 fps**, it provides reliable, distortion-free imaging for demanding machine-vision applications such as motion analysis, automation, robotics, and scientific imaging.

The camera is 100% compliant with **USB3 Vision v1.0** and **GenICam 3.x** standards, offering plug-and-play compatibility across all major software platforms.

---

## Key Features

*   **Sony IMX296 Global Shutter Sensor**: 1/2.9" CMOS sensor with 3.45 µm pixels, ensuring zero rolling distortion.
*   **High Frame Rate**: Up to 60 fps at full resolution (1456 × 1088); exceeds 1900 fps with reduced ROI.
*   **True Analog Gain**: 0–48 dB range with 0.1 dB steps for extremely low-noise imaging.
*   **Long Exposure**: Supports manual shutter with true long-exposure capability (≥ 15 seconds).
*   **Precise Synchronization**: Opto-isolated hardware trigger (5–24V) and strobe output with 0.1 µs hardware timestamping.
*   **Industrial Design**: USB 3.0 bus-powered, compact form factor, and on-board temperature monitoring.
*   **Standard Compliance**: Fully compatible with any GenICam-compliant software without proprietary patches.

---

## Specifications

| Feature | Specification |
| :--- | :--- |
| **Sensor** | Sony IMX296LLR (Monochrome, Global Shutter) |
| **Resolution** | 1456 (H) × 1088 (V), 1.58 MP |
| **Pixel Size** | 3.45 µm × 3.45 µm |
| **Max Frame Rate** | 60 fps @ Full ROI (Mono8) |
| **Exposure Time** | 29 µs to ≥ 15 seconds |
| **Analog Gain** | 0 – 48 dB (0.1 dB steps) |
| **Interface** | USB 3.0 (Type-C or Micro-B depending on model) |
| **I/O Connector** | 6-pin Hirose (Trigger In, Strobe Out, GND) |
| **Power** | USB Bus-powered, 5V / ≤ 3.2W |
| **Lens Mount** | M12 (Default) / CS-Mount (Optional) |
| **Operating Temp** | -10 °C to +65 °C |

---

## 1. Hardware

### 6-pin Hirose Connector Pinout (HR10A-7R-6P)

| Pin | Signal | Description |
| :--- | :--- | :--- |
| 1 | GPIO_B33 | Reserved |
| 2 | Trig + | Opto-isolated Trigger Input (+) |
| 3 | GPIO_A33 | Reserved |
| 4 | STROBE + | Opto-isolated Strobe Output (+) |
| 5 | STROBE - / Trig - | Opto-isolated I/O Ground |
| 6 | GND | System Ground |

For detailed hardware dimensions, connector parameters, and wiring diagrams, refer to the [U3V-CAM-IMX296 User Manual V10.pdf](./U3V-CAM-IMX296%20User%20Manual%20V10.pdf).

---

## 2. InnoMaker SDK

The **InnoMaker U3V SDK** (version 2.2.0) is the recommended choice for developers who need direct, low-level camera access and maximum performance. It provides a lightweight, cross-platform C/C++ API and a Python binding, all built on the same underlying shared library. Version 2.2.0 adds color-camera support via a pluggable ISP chain (demosaic, white balance, gamma, color correction).

**SDK version**: 2.2.0 — **Last updated**: 2026-07-02

For full delivery notes and package contents, see [`InnoMaker_SDK_Support_Win_Linux_Mac_Python/DELIVERY_OVERVIEW.md`](./InnoMaker_SDK_Support_Win_Linux_Mac_Python/DELIVERY_OVERVIEW.md).

### Available Packages

| Package | Platform | Size | Description |
| :--- | :--- | :--- | :--- |
| `u3v-sdk-2.2.0-windows-x64.zip` | Windows 10/11 x64 | 18 MB | C/C++ SDK + Qt6 viewer, fully self-contained |
| `u3v-sdk-2.2.0-linux-x64.tar.gz` | Ubuntu 22.04+ x64 | 150 KB | C/C++ SDK + viewer (system Qt6 + libusb) |
| `u3v-sdk-2.2.0-linux-arm64.tar.gz` | Pi 5 / Jetson Orin Nano | 150 KB | ARM64 counterpart of the Linux x64 package |
| `u3v-sdk-2.2.0-macos-arm64.zip` | Apple Silicon (M1–M4) | 55 MB | C/C++ SDK + `u3v_viewer.app` |
| `u3v-sdk-2.2.0-macos-x64.zip` | Intel Mac | 55 MB | Intel counterpart of the macOS arm64 package |
| `u3v-sdk-2.2.0-python.zip` | All platforms | 1.1 MB | Python 3.8+ package; bundles native libraries for every platform |

All packages share the same underlying SDK. Code written against the C API on Windows compiles and runs unchanged on Linux and macOS. The Python package wraps the identical C library via ctypes — no separate codepath.

### Supported Platforms

| Platform | Architecture | Status |
| :--- | :--- | :--- |
| Windows 10 / 11 | x64 | ✅ Fully Supported |
| Ubuntu 22.04+ / Debian 12+ | x64 | ✅ Fully Supported |
| Raspberry Pi 5 (Bookworm / Trixie) | ARM64 | ✅ Fully Supported |
| NVIDIA Jetson Orin Nano (JetPack 6.0+) | ARM64 | ✅ Fully Supported |
| macOS 11+ (Apple Silicon M1/M2/M3/M4) | arm64 | ✅ Fully Supported |
| macOS 11+ (Intel Mac) | x86_64 | ✅ Fully Supported |

### Windows Installation (C/C++)

1. Extract `u3v-sdk-2.2.0-windows-x64.zip` to any directory (e.g., `D:\u3v\`)
2. Plug in the U3V camera
3. Run `tools\zadig-2.9.exe` as administrator → select the camera → choose **WinUSB** → click **Install Driver**
4. Double-click `bin\u3v_viewer.exe` to launch the GUI viewer

For application development, add `include\` to your include path, `lib\u3v_cam.lib` to your linker, and ensure `bin\` is on PATH at runtime.

### Linux Installation (C/C++)

```bash
# Extract the matching package
tar xzf u3v-sdk-2.2.0-linux-x64.tar.gz      # x86_64
# or
tar xzf u3v-sdk-2.2.0-linux-arm64.tar.gz    # Pi 5 / Jetson Orin Nano
cd u3v-sdk-2.2.0-linux-*

# Install runtime dependencies (one-time)
sudo apt update && sudo apt install -y libusb-1.0-0 libqt6widgets6

# Install udev rule for non-root USB access (one-time)
sudo tee /etc/udev/rules.d/99-u3v.rules > /dev/null <<'EOF'
SUBSYSTEM=="usb", ATTRS{bDeviceClass}=="ef", ATTRS{bDeviceSubClass}=="02", ATTRS{bDeviceProtocol}=="01", MODE="0666", GROUP="plugdev"
EOF
sudo udevadm control --reload-rules && sudo udevadm trigger
sudo usermod -aG plugdev $USER
# Log out and back in for the group change to take effect

# Launch the GUI viewer
./run_viewer.sh
```

For application development:

```bash
gcc my_app.c \
    -I ./include \
    -L ./lib -lu3v_cam \
    -Wl,-rpath,'$ORIGIN/lib' \
    -o my_app
```

### Python SDK (u3v-sdk-2.2.0-python)

The Python package targets **Python 3.8–3.12** on all five supported platforms (Windows, Linux x64/ARM64, macOS arm64/x64). It is ideal for ML / computer-vision workflows, rapid prototyping, headless capture scripts, and Jupyter notebooks.

**Windows:**
```bat
:: 1. Extract u3v-sdk-2.2.0-python.zip
:: 2. Install WinUSB driver (use Zadig from u3v-sdk-2.2.0-windows-x64\tools\)
:: 3. Open a command prompt in u3v-sdk-2.2.0-python\
install_deps.bat        :: installs numpy + optional viewer/cv2
run_basic_capture.bat   :: smoke test
run_viewer.bat          :: live PyQt6 viewer
```

**Linux (Ubuntu 22.04, Debian 12+, Raspberry Pi OS Bookworm):**
```bash
# 1. Install runtime dependencies
sudo apt update && sudo apt install -y libusb-1.0-0 python3-pip

# 2. Install udev rule for non-root USB access (one-time, required)
sudo tee /etc/udev/rules.d/99-u3v.rules > /dev/null <<'EOF'
SUBSYSTEM=="usb", ATTRS{bDeviceClass}=="ef", ATTRS{bDeviceSubClass}=="02", ATTRS{bDeviceProtocol}=="01", MODE="0666", GROUP="plugdev"
EOF
sudo udevadm control --reload-rules && sudo udevadm trigger
sudo usermod -aG plugdev $USER
# Log out and back in for the group change to take effect

# 3. Extract and install the Python SDK
unzip u3v-sdk-2.2.0-python.zip
cd u3v-sdk-2.2.0-python
chmod +x *.sh
./install_deps.sh
./run_basic_capture.sh
```

> **Important:** The udev rule in step 2 is required. Without it, the SDK will fail with a permission or protocol error when opening the camera, even if the device is detected.

**Python API example:**
```python
import u3v_cam

# Discover cameras
print(u3v_cam.list_cameras())

# Open, configure, capture
with u3v_cam.Camera() as cam:
    cam.set_roi(1456, 1088)
    cam.pixel_format = u3v_cam.PFNC_MONO8
    cam.start()
    cam.exposure_us  = 5000
    cam.gain         = 0
    cam.frame_rate   = 60
    for _ in range(60):
        frame = cam.read_frame()   # numpy.ndarray (H, W) uint8
    cam.stop()
```

**Optional dependencies:**

| Feature | Package | Install |
| :--- | :--- | :--- |
| Core (required) | `numpy>=1.20` | `pip install numpy` |
| PyQt6 live viewer | `PyQt6` + `pyqtgraph` | `pip install PyQt6 pyqtgraph` |
| Live preview | `opencv-python` | `pip install opencv-python` |

### macOS Installation (C/C++)

Pick the archive that matches your Mac:

```bash
# 1. Extract the matching archive
unzip u3v-sdk-2.2.0-macos-arm64.zip    # Apple Silicon (M1/M2/M3/M4)
# OR
unzip u3v-sdk-2.2.0-macos-x64.zip     # Intel Mac

# 2. Install libusb (one-time; required for both GUI and SDK)
#    Install Homebrew first if needed: https://brew.sh/
brew install libusb

# 3. Run the GUI viewer
cd u3v-sdk-2.2.0-macos-*
open bin/u3v_viewer.app
```

> **Gatekeeper note**: If macOS blocks the unsigned app, right-click → **Open** → **Open Anyway** (first time only).

For application development:

```bash
clang my_app.c \
    -I ./include \
    -L ./lib -lu3v_cam \
    -Wl,-rpath,@loader_path/lib \
    -o my_app
```

### C API Overview

The following signatures are extracted directly from the SDK headers (`include/u3v/`):

```c
/* Initialization */
u3v_status_t u3v_sdk_init(void);
void         u3v_sdk_shutdown(void);

/* Discovery */
int          u3v_discover(u3v_device_info_t *info, int max_devices);

/* Camera control */
u3v_status_t u3v_camera_open(u3v_camera_t **cam, int device_index);
void         u3v_camera_close(u3v_camera_t *cam);
u3v_status_t u3v_camera_set_exposure(u3v_camera_t *cam, uint32_t time_us);
u3v_status_t u3v_camera_get_exposure(u3v_camera_t *cam, uint32_t *time_us);
u3v_status_t u3v_camera_set_gain(u3v_camera_t *cam, uint32_t gain);
u3v_status_t u3v_camera_get_gain(u3v_camera_t *cam, uint32_t *gain);
u3v_status_t u3v_camera_set_trigger_mode(u3v_camera_t *cam, uint32_t mode);
u3v_status_t u3v_camera_get_trigger_mode(u3v_camera_t *cam, uint32_t *mode);
u3v_status_t u3v_camera_send_software_trigger(u3v_camera_t *cam);
u3v_status_t u3v_camera_start(u3v_camera_t *cam);
u3v_status_t u3v_camera_stop(u3v_camera_t *cam);

/* Streaming */
u3v_status_t u3v_stream_create(u3v_stream_t **stream, u3v_camera_t *cam,
                                const u3v_stream_config_t *config);
void         u3v_stream_destroy(u3v_stream_t *stream);
u3v_status_t u3v_buffer_alloc(u3v_buffer_t *buf, uint32_t size);
void         u3v_buffer_free(u3v_buffer_t *buf);
u3v_status_t u3v_stream_grab(u3v_stream_t *stream, u3v_buffer_t *buf);
u3v_status_t u3v_buffer_save(const u3v_buffer_t *buf, const char *filename);
```

For the complete API reference, see the headers in `include/u3v/` inside the SDK package.

---

## 3. Windows Driver Switching (InnoMaker SDK ↔ eBUS Player)

On Windows, the **InnoMaker SDK** uses the **WinUSB** driver, while **eBUS Player / eBUS SDK** installs its own **Pleora USB** driver. These two drivers are mutually exclusive — only one can be active at a time for the same device.

When switching between the two, follow these steps:

**Step 1: Uninstall the current driver**

1. Open **Device Manager** (`Win + X` → Device Manager)
2. Locate the camera under **Universal Serial Bus devices** or **Imaging devices**
3. Right-click the device → **Uninstall device**
4. Check **Delete the driver software for this device** (if prompted), then click **Uninstall**
5. Unplug the camera and plug it back in

**Step 2: Install the target driver**

- **Switch to InnoMaker SDK (WinUSB):**
  Run `tools\zadig-2.9.exe` from the `u3v-sdk-2.2.0-windows-x64` package, select the camera device, choose **WinUSB**, and click **Install Driver**.

- **Switch to eBUS Player (Pleora USB):**
  Launch the eBUS SDK installer or eBUS Player — it will automatically install the Pleora USB driver on first run.

> **Tip:** If the camera is not recognized after switching, try unplugging and replugging the USB cable, or restart the computer.

---

## 4. Third Party SDK

The U3V-CAM-IMX296 is 100% USB3 Vision / GenICam compliant and works out-of-the-box with any standard-compliant software. The following third-party tools are verified and supported.

### eBus Player (Pleora / JAI)

**eBUS Player** is a full-featured GUI application for controlling GenICam-compliant cameras. It supports live preview, camera parameter adjustment, data logging, and multi-camera management. The **eBUS SDK** additionally provides C++ and Python APIs for custom application development.

**When to use eBUS:**
- You prefer a GUI over programmatic control
- You need multi-camera management in a vendor-neutral environment
- You are integrating with existing GenICam / GigE Vision workflows

**Supported platforms**: Windows 10/11, Linux x86_64, Linux ARM64 (Raspberry Pi 4/5 Bookworm)

**Downloads (official latest):**
- Windows: [https://www.jai.com/support-software/jetson-ubuntu](https://www.jai.com/support-software/jetson-ubuntu)
- Linux: [https://www.jai.com/support-software/jetson-ubuntu](https://www.jai.com/support-software/jetson-ubuntu)

**Local package for Raspberry Pi 5** (pre-downloaded, no internet required):
- [`eBusPlayer&Aravis_PI5_Linux/ebus_for_raspberry_pi5/`](./eBusPlayer%26Aravis_PI5_Linux/ebus_for_raspberry_pi5/) — `.deb` installer + quick start guides (C++, .NET, Python, Linux, Pi 4/5)

**Installation on Raspberry Pi 5 (Bookworm):**
```bash
sudo apt install -y qtbase5-dev qt5-qmake
sudo dpkg -i eBUS_SDK_JAI_Raspberry_Pi4_Pi5_linux-aarch64-arm-6.5.3-7155.deb
sudo reboot
```

> **Note**: eBUS SDK requires a Pleora license for production use. Without a license, the viewer and SDK operate in evaluation mode (watermarked output, limited streaming duration).

### Aravis (Open-Source GenICam)

**Aravis** is an open-source GenICam implementation for Linux (GNOME Project), providing a lightweight alternative to eBUS for custom application development.

**Local package for Raspberry Pi 5:**
- [`eBusPlayer&Aravis_PI5_Linux/aravis_for_raspberry_pi5/aravis-0.8.35.tar.xz`](./eBusPlayer%26Aravis_PI5_Linux/aravis_for_raspberry_pi5/)

**License**: LGPL — free for commercial use.

### Pre-installed OS Image for Raspberry Pi 5

A pre-configured Raspberry Pi 5 OS image with all software (InnoMaker SDK, eBUS Player, Aravis) pre-installed is available for download:

**Download**: [https://www.jianguoyun.com/p/DXuEVqMQpdSrBxiqmp0GIAA](https://www.jianguoyun.com/p/DXuEVqMQpdSrBxiqmp0GIAA)  
**Password**: `uwpui3`

**Contents:**
- Preset OS image for Raspberry Pi 5 (ready to flash)
---

## Repository Structure

| Directory / File | Purpose |
| :--- | :--- |
| [`InnoMaker_SDK_Support_Win_Linux_Mac_Python/`](./InnoMaker_SDK_Support_Win_Linux_Mac_Python/) | InnoMaker U3V SDK v2.2.0 packages (Windows, Linux x64/ARM64, macOS, Python) |
| [`InnoMaker_SDK_Support_Win_Linux_Mac_Python/DELIVERY_OVERVIEW.md`](./InnoMaker_SDK_Support_Win_Linux_Mac_Python/DELIVERY_OVERVIEW.md) | Detailed SDK delivery notes and cross-platform reference |
| [`InnoMaker_SDK_Support_Win_Linux_Mac_Python/RELEASE_NOTES.md`](./InnoMaker_SDK_Support_Win_Linux_Mac_Python/RELEASE_NOTES.md) | SDK changelog and version history |
| [`eBusPlayer&Aravis_PI5_Linux/`](./eBusPlayer%26Aravis_PI5_Linux/) | eBUS Player and Aravis packages for Raspberry Pi 5 / Linux |
| [`eBusPlayer_Win/`](./eBusPlayer_Win/) | Windows eBUS Player download links |
| [`PreInstalled-IMG-PI5/`](./PreInstalled-IMG-PI5/) | Download link for pre-configured Raspberry Pi 5 OS image |
| [`images/`](./images/) | Product images |
| [`U3V-CAM-IMX296 User Manual V10.pdf`](./U3V-CAM-IMX296%20User%20Manual%20V10.pdf) | Complete hardware and software documentation |

---

## FAQ & Troubleshooting

### AcquisitionFrameRate has no effect — camera always runs at ~60 fps

The IMX296 sensor controls frame rate through **exposure time**, not through the `AcquisitionFrameRate` GenICam feature. The `AcquisitionFrameRate` register is present in the camera's XML manifest but is not implemented in firmware and has no effect on actual output frame rate.

**To reduce frame rate, set a longer exposure time:**

| Target Frame Rate | Minimum ExposureTime |
| :--- | :--- |
| 30 fps | > 33,333 µs |
| 10 fps | > 100,000 µs |
| 5 fps | > 200,000 µs |
| 1 fps | > 1,000,000 µs |

When the exposure time exceeds one frame period, the sensor automatically extends the frame interval to accommodate the exposure, effectively reducing the frame rate.

> **Important:** Exposure time, gain, and frame rate must be set **after** `cam.start()`. ROI and pixel format are configured before `start()`. After changing exposure to long-exposure mode, the new frame rate takes effect immediately on the next frame.

**Example (InnoMaker Python SDK):**
```python
with u3v_cam.Camera() as cam:
    cam.set_roi(1456, 1088)
    cam.pixel_format = u3v_cam.PFNC_MONO8
    cam.start()
    cam.exposure_us = 200000   # 200 ms → ~5 fps; set after start()
    cam.gain        = 0
    frame = cam.read_frame()
    cam.stop()
```

**Example (Aravis Python):**
```python
cam.set_exposure_time(200000)   # 200 ms
cam.set_region(0, 0, 1456, 1088)  # re-apply ROI
```

---

## Support

*   **Website**: [www.inno-maker.com](https://www.inno-maker.com)
*   **GitHub**: [github.com/INNO-MAKER](https://github.com/INNO-MAKER)
*   **Email**: [support@inno-maker.com](mailto:support@inno-maker.com) | [sales@inno-maker.com](mailto:sales@inno-maker.com) | [zoujy@inno-maker.com](mailto:zoujy@inno-maker.com)
