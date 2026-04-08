# U3V-CAM-IMX296 USB3 Vision Industrial Camera

![U3V-CAM-IMX296](https://www.inno-maker.com/wp-content/uploads/2021/06/IMX296-MIPI-4.jpg "IMX296")

The **U3V-CAM-IMX296** is a high-performance USB3 Vision industrial camera featuring the **Sony IMX296LLR** (monochrome) global shutter CMOS sensor. With a resolution of **1.58 MP (1456 × 1088)** and a full-resolution frame rate of **60 fps**, it provides reliable, distortion-free imaging for demanding machine-vision applications such as motion analysis, automation, robotics, and scientific imaging.

The camera is 100% compliant with **USB3 Vision v1.0** and **GenICam 3.x** standards, offering plug-and-play compatibility across all major software platforms including OpenSource Aravis and Pleora eBus Universal.

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

## Software & SDK

### 1. Standard USB3 Vision Software (eBus Player)
The camera works out-of-the-box with any U3V-compliant software. We recommend the latest **eBus Player** for the best experience:
*   **For Windows**: [Download eBus Player for Win](https://www.jai.com/support-software/jetson-ubuntu)
*   **For Linux**: [Download eBus Player for Linux](https://www.jai.com/support-software/jetson-ubuntu)

### 2. U3V Camera SDK (C-based API)
For developers looking to integrate the camera into their own applications, we provide a lightweight C-based SDK.
*   **Location**: [`LIBUSB&QT/`](./LIBUSB&QT/)
*   **Features**: Discovery, Parameter Control (Exposure, Gain, ROI, Trigger), and High-speed Streaming.
*   **Documentation**: See [`LIBUSB&QT/README.md`](./LIBUSB&QT/README.md) for API usage and examples.
*   **Build Environment**: See [`LIBUSB&QT/BUILD_ENVIRONMENT.md`](./LIBUSB&QT/BUILD_ENVIRONMENT.md) for Windows, Linux, and macOS setup.

---

## Installation Guide

### Windows
1.  **Drivers**: For standard U3V software, use the eBus Driver Installation Tool. For the C-based SDK, use **Zadig** to install the **WinUSB** driver on the **Composite Parent** device. (See [Driver Guide](./LIBUSB&QT/WINUSB_DRIVER_INSTALL.md)).
2.  **SDK**: Download the pre-compiled binaries from [`LIBUSB&QT/Release.zip`](./LIBUSB&QT/Release.zip).

### Linux (including Raspberry Pi 5)
The camera is fully validated on Raspberry Pi 5 (Debian Bookworm/Trixie).

#### Option A: Use Preset Image (Recommended)
For a quick setup, we provide a pre-configured OS image for Raspberry Pi 5 with all drivers and software pre-installed:
*   **Download Link**: [Preset IMG (Flash and Boot) For PI5, Windows SDK](https://www.jianguoyun.com/p/DXuEVqMQpdSrBxiqmp0GIAA)
*   **Password**: `uwpui3`

#### Option B: Manual Installation
1.  **Install eBus SDK**:
    ```bash
    cd "eBusPlayer&Aravis_PI5_Linux"
    sudo dpkg -i eBUS_SDK_JAI_Raspberry_Pi4_Pi5_linux-aarch64-arm-6.5.3-7155.deb
    ```
2.  **Dependencies**: `sudo apt install libqt5opengl5 libusb-1.0-0-dev`
3.  **Udev Rules**: To access the camera without root, add the following to `/etc/udev/rules.d/99-u3v.rules`:
    ```
    SUBSYSTEM=="usb", ATTR{idVendor}=="04b4", ATTR{idProduct}=="1010", MODE="0666"
    ```

---

## Repository Structure

*   [`LIBUSB&QT/`](./LIBUSB&QT/): C-based SDK, WinUSB drivers, and development guides.
*   [`eBusPlayer&Aravis_PI5_Linux/`](./eBusPlayer&Aravis_PI5_Linux/): Linux SDK, udev rules, and Raspberry Pi trigger scripts.
*   [`eBusPlayer_Win/`](./eBusPlayer_Win/): Windows eBus SDK information.
*   [`U3V-CAM-IMX296 User Manual V1.pdf`](./U3V-CAM-IMX296%20User%20Manual%20V1.pdf): Comprehensive technical documentation.

---

## Support

For more information and technical support, please visit:
*   **Website**: [www.inno-maker.com](https://www.inno-maker.com)
*   **GitHub**: [github.com/inno-maker](https://github.com/inno-maker)
*   **Email**: [support@inno-maker.com](mailto:support@inno-maker.com) | [sales@inno-maker.com](mailto:sales@inno-maker.com)
