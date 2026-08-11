# U3V Camera SDK — What's New

A customer-facing summary of improvements by version. Each entry
describes what got better from a usage standpoint. For the current
version's full feature list, see `RELEASE_NOTES.md`.

---

## 2.2.6

- **More reliable frame capture, especially with triggering and multiple
  cameras.** The library now reports incomplete frames so your application can
  skip them and work only with complete images. Image streaming is more robust
  under heavy load and in hardware-trigger mode.
- **Color capture from the Python interface.** Enable color with
  `cam.enable_color()`, and `read_frame()` returns a demosaiced `(H, W, 3)`
  RGB image — the same color output as the viewer.

## 2.2.5

- **Read back trigger and exposure settings.** Your application can now
  query the values it configured — exposure-auto, trigger activation,
  line debounce, and strobe timing — directly from the camera, in both
  C and Python.
- **Readable names for trigger configuration.** Trigger activation,
  source, and selector can be set using descriptive named constants
  instead of numeric values, making trigger setup code clearer and less
  error-prone.
- **Faster, more stable color processing.** The color ISP RGB filter uses
  much less CPU, improving color live-view stability on ARM hosts. For
  smooth full-resolution color ISP, a Jetson Orin Nano or x86_64 host is
  recommended (see the delivery guide for host recommendations).

## 2.2.3

- **Lower CPU usage in live view on Linux.** Real-time preview in the
  viewer is significantly lighter on CPU, especially on Raspberry Pi 5
  and other ARM64 hosts. Long-running single-camera preview stays
  smooth and responsive.
- **Out-of-the-box driver setup on Windows.** The Windows package now
  includes the USB driver installer and a step-by-step installation
  guide, so a new machine can be brought online without any separate
  download.
- **External trigger usage guide.** A new guide covers wiring and
  host-side code for hardware triggering — Arduino, Raspberry Pi,
  NVIDIA Jetson, STM32, TTL function generators, and 24 V PLC outputs —
  plus SDK-side trigger configuration in C and Python.

## 2.2.1 – 2.2.2

- Maintenance and stability improvements.

## 2.2.0

- **Color camera support.** Color (Bayer) cameras such as the IMX296
  color variant produce a viewable RGB preview out of the box, with
  in-viewer controls for white balance, gamma, and color correction.
- **Extensible image-processing framework.** Optional processing
  plugins can be added to the pipeline; the viewer shows their controls
  automatically. Mono workflows are unaffected.

## 2.1.1

- **More stable multi-camera streaming on Linux.** Running two cameras
  at once is now stable across long sessions — a higher-rate camera no
  longer stutters when a slower camera streams alongside it. Most
  noticeable on ARM64 hosts (Jetson Orin Nano, Raspberry Pi 5).

## 2.1.0

- **Multiple cameras at once.** Enumerate and open several cameras
  concurrently, each addressed by index, with a device-selection dialog
  in the viewer.
- **More sensors supported out of the box** — Sony IMX296, OmniVision
  OV9281, and Basler-pattern cameras.
- **Native Raspberry Pi 5 and Jetson Orin Nano builds.**
