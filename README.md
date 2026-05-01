# U3V-CAM-IMX296 USB3 Vision Industrial Camera

![U3V-CAM-IMX296](./images/amazon_main_4.png "U3V-CAM-IMX296")

The **U3V-CAM-IMX296** is a high-performance USB3 Vision industrial camera featuring the **Sony IMX296LLR** (monochrome) global shutter CMOS sensor. With a resolution of **1.58 MP (1456 × 1088)** and a full-resolution frame rate of **60 fps**, it provides reliable, distortion-free imaging for demanding machine-vision applications such as motion analysis, automation, robotics, and scientific imaging.

The camera is 100% compliant with **USB3 Vision v1.0** and **GenICam 3.x** standards, offering plug-and-play compatibility across all major software platforms.

---

## Key Features

*   **Sony IMX296 Global Shutter Sensor**: 1/2.9" CMOS sensor with 3.45 µm pixels, ensuring zero rolling distortion.
*   **High Frame Rate**: Up to 60 fps at full resolution (1456 x 1088); exceeds 1900 fps with reduced ROI.
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

## Software & SDK Options

### 1. **InnoMaker U3V SDK** (Recommended for Custom Development)

Our proprietary **U3V SDK** provides a lightweight, cross-platform C-based API with full camera control and high-speed image streaming. This is the recommended choice for developers who need direct, low-level camera access and maximum performance.

**Key Advantages**:
- **Lightweight & Fast**: Minimal overhead, optimized for real-time applications
- **Cross-Platform**: Identical API on Windows, Linux (x64/ARM64), Raspberry Pi, and Jetson
- **Full Control**: Direct access to all camera parameters (exposure, gain, ROI, trigger, strobe)
- **Easy Integration**: Simple C API with comprehensive examples
- **Binary Distribution**: Pre-built, ready-to-use packages for Windows and Linux

**Supported Platforms**:
| Platform | Architecture | Status |
|---|---|---|
| Windows | x64 (Visual Studio 2019+) | ✅ Fully Supported |
| Ubuntu / Debian | x64 | ✅ Fully Supported |
| Raspberry Pi 5 | ARM64 (Bookworm/Trixie) | ✅ Fully Supported |
| NVIDIA Jetson Orin Nano | ARM64 (JetPack 6.0+) | ✅ Fully Supported |

**SDK Contents**:
- Pre-built libraries (DLL on Windows, .so on Linux)
- Qt6-based GUI viewer for quick testing
- CLI demo application with source code
- Complete C API headers (9 header files)
- Cross-platform build examples

**Getting Started**: See [InnoMaker SDK Installation & Usage](#innomaker-sdk-installation--usage) below.

---

### 2. **Standard USB3 Vision Software (eBus Player & Aravis)**

For users who prefer industry-standard, GenICam-compliant tools, we provide integration with:

- **eBus Player** (Pleora): Full-featured GUI with advanced camera control, data logging, and multi-camera support
- **Aravis** (GNOME Project): Open-source GenICam implementation for Linux

Both tools work out-of-the-box with the U3V-CAM-IMX296 without any custom drivers or patches.

**When to Use**:
- You prefer a GUI over programmatic control
- You need multi-camera management
- You want vendor-neutral, open-source solutions
- You're integrating with existing GenICam workflows

**Resources**: See [Standard USB3 Vision Software](#standard-usb3-vision-software-ebus-player--aravis) section below.

---

## Hardware Interface

### 6-pin Hirose Connector Pinout (HR10A-7R-6P)

| Pin | Signal | Description |
| :--- | :--- | :--- |
| 1 | GPIO_B33 | Reserved |
| 2 | Trig + | Opto-isolated Trigger Input (+) |
| 3 | GPIO_A33 | Reserved |
| 4 | STROBE + | Opto-isolated Strobe Output (+) |
| 5 | STROBE - / Trig - | Opto-isolated I/O Ground |
| 6 | GND | System Ground |

---

## InnoMaker SDK Installation & Usage

### Windows Installation

#### Step 1: Extract the SDK

Download `V9-SDK-DLL-CUS.zip` from the [Resource Downloads](#resource-downloads) section and extract to any directory (e.g., `D:\u3v\`).

#### Step 2: Install WinUSB Driver

1. Plug in the U3V camera
2. Navigate to the extracted folder: `D:\u3v\V9-SDK-DLL-CUS\tools\`
3. Right-click `zadig-2.9.exe` and select **Run as administrator**
4. Follow the on-screen prompts to install the **WinUSB driver**
5. For detailed driver instructions, see `V9-SDK-DLL-CUS\WINUSB_DRIVER_INSTALL.md`

#### Step 3: Launch the GUI Viewer

Double-click `D:\u3v\V9-SDK-DLL-CUS\run_viewer.bat` to launch the GUI viewer. Your camera should appear in the device list.

#### Step 4: Application Development (Optional)

To integrate the SDK into your own Visual Studio project:

```cpp
// Header
#include <u3v/u3v_sdk.h>

// Visual Studio project settings:
//   Additional Include Directories:  V9-SDK-DLL-CUS\include
//   Additional Library Directories:  V9-SDK-DLL-CUS\lib
//   Additional Dependencies:         u3v_cam.lib

// At runtime, ensure V9-SDK-DLL-CUS\bin is on PATH, or copy the
// contents of bin\ to your executable's directory.
```

---

### Linux Installation (Ubuntu / Debian / Raspberry Pi)

#### Step 1: Extract the SDK

```bash
tar xzf V9-SDK-SO-CUS.tar.gz
cd V9-SDK-SO-CUS
```

#### Step 2: Install Runtime Dependencies

```bash
sudo apt update
sudo apt install -y libusb-1.0-0 libqt6widgets6
```

#### Step 3: Setup USB Permissions (One-Time)

Install the udev rule to allow non-root USB camera access:

```bash
sudo tee /etc/udev/rules.d/99-u3v.rules > /dev/null <<'EOF'
SUBSYSTEM=="usb", ATTRS{bDeviceClass}=="ef", ATTRS{bDeviceSubClass}=="02", ATTRS{bDeviceProtocol}=="01", MODE="0666", GROUP="plugdev"
EOF

sudo udevadm control --reload-rules && sudo udevadm trigger
sudo usermod -aG plugdev $USER
# Log out and back in for the group change to take effect
```

#### Step 4: Launch the GUI Viewer

Determine your system architecture and launch the viewer:

```bash
# For x86_64 (Intel/AMD)
cd ubuntu22.04-x64 && ./run_viewer.sh

# For ARM64 (Raspberry Pi 5, Jetson Orin Nano, etc.)
cd ubuntu22.04-arm64 && ./run_viewer.sh
```

#### Step 5: Application Development (Optional)

Compile your own application against the SDK:

```bash
ARCH_DIR=ubuntu22.04-x64       # or ubuntu22.04-arm64

gcc my_app.c \
    -I$ARCH_DIR/include \
    -L$ARCH_DIR/lib -lu3v_cam \
    -Wl,-rpath,'$ORIGIN/lib' \
    -o my_app

./my_app
```

The `-Wl,-rpath,'$ORIGIN/lib'` flag embeds the library search path, so you can distribute `my_app` together with the `lib/` folder without installing system-wide.

---

## InnoMaker SDK API Overview

The SDK provides a simple C API for camera control and image acquisition.

### Initialization & Discovery

```c
/* Initialize the SDK */
u3v_status_t u3v_sdk_init(void);
void         u3v_sdk_shutdown(void);

/* Discover connected cameras */
int u3v_discover(u3v_device_info_t *info, int max_devices);
```

### Camera Control

```c
/* Open and close camera */
u3v_status_t u3v_camera_open(u3v_camera_t **cam, int device_index);
void         u3v_camera_close(u3v_camera_t *cam);

/* Start/stop acquisition */
u3v_status_t u3v_camera_start(u3v_camera_t *cam);
u3v_status_t u3v_camera_stop(u3v_camera_t *cam);

/* Parameter control (examples) */
u3v_status_t u3v_camera_set_exposure(u3v_camera_t *cam, uint32_t microseconds);
u3v_status_t u3v_camera_set_gain(u3v_camera_t *cam, uint32_t gain);
u3v_status_t u3v_camera_set_width(u3v_camera_t *cam, uint32_t width);
u3v_status_t u3v_camera_set_height(u3v_camera_t *cam, uint32_t height);
u3v_status_t u3v_camera_set_trigger_mode(u3v_camera_t *cam, uint32_t mode);
u3v_status_t u3v_camera_send_software_trigger(u3v_camera_t *cam);
```

### Image Streaming

```c
/* Create and destroy stream */
u3v_status_t u3v_stream_create(u3v_stream_t **stream, u3v_camera_t *cam, const u3v_stream_config_t *config);
void         u3v_stream_destroy(u3v_stream_t *stream);

/* Grab frames */
u3v_status_t u3v_stream_grab(u3v_stream_t *stream, u3v_frame_t *frame, uint32_t timeout_ms);

/* Buffer management */
u3v_status_t u3v_buffer_alloc(u3v_buffer_t *buf, uint32_t size);
void         u3v_buffer_free(u3v_buffer_t *buf);
u3v_status_t u3v_buffer_save(u3v_buffer_t *buf, const char *filename);
```

For the complete API reference, see the headers in `include/u3v/`.

---

## Basic Usage Example (C)

```c
#include <u3v/u3v_sdk.h>
#include <stdio.h>

int main(void) {
    /* 1. Initialize SDK */
    if (u3v_sdk_init() != U3V_OK) {
        printf("SDK initialization failed.\n");
        return -1;
    }

    /* 2. Discover cameras */
    u3v_device_info_t devices[8];
    int count = u3v_discover(devices, 8);
    if (count == 0) {
        printf("No cameras found.\n");
        u3v_sdk_shutdown();
        return 0;
    }
    printf("Found camera: %s %s\n", devices[0].manufacturer, devices[0].model);

    /* 3. Open camera */
    u3v_camera_t *cam = NULL;
    if (u3v_camera_open(&cam, 0) != U3V_OK) {
        printf("Failed to open camera.\n");
        u3v_sdk_shutdown();
        return -1;
    }

    /* 4. Configure camera */
    u3v_camera_set_exposure(cam, 10000);  /* 10ms */
    u3v_camera_set_gain(cam, 0);          /* 0 dB */

    /* 5. Create stream */
    u3v_stream_t *stream = NULL;
    u3v_stream_config_t cfg = {
        .num_buffers = 4,
        .timeout_ms = 1000,
        .transfer_size = 0  /* Auto */
    };
    u3v_stream_create(&stream, cam, &cfg);

    /* 6. Start acquisition */
    u3v_camera_start(cam);

    /* 7. Grab a frame */
    uint32_t payload_size = 0;
    u3v_camera_get_payload_size(cam, &payload_size);
    u3v_buffer_t buf = {0};
    u3v_buffer_alloc(&buf, payload_size);

    u3v_status_t status = u3v_stream_grab(stream, &buf, 1000);
    if (status == U3V_OK && buf.status == 0) {
        printf("Captured frame: %d x %d\n", buf.width, buf.height);
        u3v_buffer_save(&buf, "image.pgm");
    }

    /* 8. Cleanup */
    u3v_buffer_free(&buf);
    u3v_camera_stop(cam);
    u3v_stream_destroy(stream);
    u3v_camera_close(cam);
    u3v_sdk_shutdown();

    return 0;
}
```

---

## Standard USB3 Vision Software (eBus Player & Aravis)

The camera works out-of-the-box with any U3V-compliant software. We provide integration packages for:

### eBus Player (Pleora)

A comprehensive GUI for camera control, data logging, and multi-camera management.

**For Windows**: [Download eBus Player for Windows](https://www.jai.com/support-software/jetson-ubuntu)

**For Linux**: [Download eBus Player for Linux](https://www.jai.com/support-software/jetson-ubuntu)

See [`eBusPlayer_Win/`](./eBusPlayer_Win/) and [`eBusPlayer&Aravis_PI5_Linux/ebus_for_raspberry_pi5/`](./eBusPlayer&Aravis_PI5_Linux/ebus_for_raspberry_pi5/) for quick start guides.

### Aravis (Open-Source GenICam)

An open-source GenICam implementation for Linux. Ideal for integration into custom applications or as a lightweight alternative to eBus Player.

**Package Location**: [`eBusPlayer&Aravis_PI5_Linux/aravis_for_raspberry_pi5/`](./eBusPlayer&Aravis_PI5_Linux/aravis_for_raspberry_pi5/)

---

## Repository Structure

| Directory | Purpose |
| :--- | :--- |
| [`InnoMaker_SDK_Libusb_Win_Linux/`](./InnoMaker_SDK_Libusb_Win_Linux/) | **InnoMaker U3V SDK** — Pre-built binaries, headers, examples, and build guides for Windows (x64) and Linux (x64/ARM64) |
| [`InnoMaker_SDK_Libusb_Win_Linux/DELIVERY_OVERVIEW.md`](./InnoMaker_SDK_Libusb_Win_Linux/DELIVERY_OVERVIEW.md) | Detailed SDK delivery notes, package contents, and cross-platform reference |
| [`eBusPlayer&Aravis_PI5_Linux/`](./eBusPlayer&Aravis_PI5_Linux/) | eBus Player and Aravis software packages for Raspberry Pi 5 and Linux systems |
| [`eBusPlayer_Win/`](./eBusPlayer_Win/) | Windows eBus Player SDK information and download links |
| [`PreInstalled-IMG-PI5/`](./PreInstalled-IMG-PI5/) | Download links for pre-configured Raspberry Pi 5 OS image with all software pre-installed |
| [`images/`](./images/) | Product images and marketing materials |
| [`U3V-CAM-IMX296 User Manual V10.pdf`](./U3V-CAM-IMX296%20User%20Manual%20V10.pdf) | Comprehensive technical documentation covering hardware, software, and troubleshooting |

---

## Quick Start Comparison

| Task | InnoMaker SDK | eBus Player | Aravis |
|---|---|---|---|
| **GUI Viewer** | ✅ Included | ✅ Full-featured | ✅ Lightweight |
| **Custom Development** | ✅ Recommended | ⚠️ Limited API | ✅ GenICam API |
| **Real-Time Performance** | ✅ Optimized | ⚠️ GUI overhead | ⚠️ GUI overhead |
| **Cross-Platform** | ✅ Windows + Linux | ✅ Windows + Linux | ✅ Linux only |
| **Learning Curve** | ✅ Simple C API | ⚠️ Steeper | ⚠️ Steeper |
| **License** | Proprietary | Commercial | Open-source (LGPL) |

---

## Support

For more information and technical support, please visit:

*   **Website**: [www.inno-maker.com](https://www.inno-maker.com)
*   **GitHub**: [github.com/INNO-MAKER](https://github.com/INNO-MAKER)
*   **Email**: [support@inno-maker.com](mailto:support@inno-maker.com) | [sales@inno-maker.com](mailto:sales@inno-maker.com)

---

## Resource Downloads

### InnoMaker U3V SDK & Preset Image for Raspberry Pi 5

**Download Link**: [U3V Camera SDK (Windows & Linux) + Preset Image for Raspberry Pi 5](https://www.jianguoyun.com/p/DXuEVqMQpdSrBxiqmp0GIAA)

**Password**: `uwpui3`

**Contents**:
- `V9-SDK-DLL-CUS.zip` — Windows SDK (x64) with GUI viewer and CLI demo
- `V9-SDK-SO-CUS.tar.gz` — Linux SDK (x64 / ARM64) with GUI viewer and CLI demo
- Preset OS image for Raspberry Pi 5 (ready to flash)

### eBus Player Official Latest Software

*   **For Windows**: [Download eBus Player for Windows](https://www.jai.com/support-software/jetson-ubuntu)
*   **For Linux**: [Download eBus Player for Linux](https://www.jai.com/support-software/jetson-ubuntu)

---

## Technical Documentation

For detailed API reference, build environment setup, driver installation, and troubleshooting:

- **SDK Overview & API**: See `InnoMaker_SDK_Libusb_Win_Linux/DELIVERY_OVERVIEW.md`
- **Windows SDK Guide**: See `InnoMaker_SDK_Libusb_Win_Linux/V9-SDK-DLL-CUS/README.md`
- **Linux SDK Guide**: See `InnoMaker_SDK_Libusb_Win_Linux/V9-SDK-SO-CUS/README_LINUX.md`
- **Hardware & Software Manual**: See `U3V-CAM-IMX296 User Manual V10.pdf`
- **eBus Player Guides**: See `eBusPlayer&Aravis_PI5_Linux/ebus_for_raspberry_pi5/` for quick start guides and API documentation
