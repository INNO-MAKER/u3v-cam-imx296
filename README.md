# U3V-CAM-IMX296 USB3 Vision Industrial Camera

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

## Software Installation

### Windows

1.  **eBus Player**: Download and install the latest version from [JAI Support](https://www.jai.com/support-software/jai-software).
2.  **Drivers**:
    *   Use the **eBus Driver Installation Tool** included with the SDK.
    *   Alternatively, for libusb-based applications, use **Zadig** to install the **WinUSB** driver on the **Composite Parent** device. Detailed instructions can be found in [WinUSB&QT/WINUSB_DRIVER_INSTALL.md](./WinUSB&QT/WINUSB_DRIVER_INSTALL.md).

### Linux (including Raspberry Pi 5)

The camera is tested on Raspberry Pi 5 (Debian Bookworm/Trixie).

1.  **Install eBus SDK**:
    ```bash
    cd "eBusPlayer&Aravis_PI5_Linux"
    sudo dpkg -i eBUS_SDK_JAI_Raspberry_Pi4_Pi5_linux-aarch64-arm-6.5.3-7155.deb
    ```
2.  **Install Dependencies**:
    ```bash
    sudo apt-get update
    sudo apt-get install libqt5opengl5
    ```
3.  **Launch eBus Player**:
    ```bash
    cd /opt/jai/ebus_sdk/linux-aarch64-arm/bin
    sudo ./eBUSPlayerJAI
    ```

---

## Repository Structure

*   `WinUSB&QT/`: Windows drivers (Zadig) and WinUSB installation guide.
*   `eBusPlayer&Aravis_PI5_Linux/`: Linux SDK, dependencies, and trigger scripts for Raspberry Pi.
*   `U3V-CAM-IMX296 User Manual V1.pdf`: Comprehensive technical documentation.

---

## Support

For more information and technical support, please visit:
*   **Website**: [www.inno-maker.com](https://www.inno-maker.com)
*   **GitHub**: [github.com/inno-maker](https://github.com/inno-maker)
*   **Email**: [support@inno-maker.com](mailto:support@inno-maker.com) | [sales@inno-maker.com](mailto:sales@inno-maker.com)
