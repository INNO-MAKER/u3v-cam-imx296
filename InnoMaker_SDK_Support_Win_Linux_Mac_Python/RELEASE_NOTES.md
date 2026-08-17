# U3V Camera SDK — Release Notes

**Version:** 2.3.0
**Date:** 2026-08-15
**Type:** Feature release (ABI-compatible)

---

## What's New

### Query the SDK version at runtime

The SDK now reports its own version and vendor through a single call, so your
application and logs can record exactly which build is in use.

```c
#include <u3v/u3v_sdk.h>

printf("SDK: %s\n", u3v_get_version());
/* e.g. "innomaker U3V Camera SDK 2.3.0" */
```

In Python:

```python
import u3v_cam
print(u3v_cam.sdk_version())   # native library, e.g. "innomaker U3V Camera SDK 2.3.0"
print(u3v_cam.__version__)     # Python wrapper version
```

On Windows the library also carries its version and vendor in the DLL file
properties (right-click `u3v_cam.dll` → Properties → Details).

### White balance from the Python interface

With the color pipeline enabled, you can apply white balance directly from
Python — a one-shot automatic balance, or fixed per-channel gains:

```python
cam.pixel_format = u3v_cam.PFNC_BAYERRG8
cam.enable_color()
cam.start()

cam.auto_white_balance()               # one-shot gray-world white balance
# or set fixed gains:  cam.set_white_balance(1.6, 1.0, 1.9)

rgb = cam.read_frame()                 # white-balanced (H, W, 3) RGB
```

`cam.get_white_balance()` returns the current red/green/blue gains. See the
Python package README for the full color workflow.

## Compatibility

**Drop-in binary replacement — no relink required for existing code.**

- C/C++: replace `libu3v_cam.so.2.2.x` with `libu3v_cam.so.2.3.0`
  (SONAME `libu3v_cam.so.2` unchanged). On Windows, replace `u3v_cam.dll`.
  On macOS, replace `libu3v_cam.dylib`.
- Python: replace the `u3v-sdk-2.3.0-python.zip` package.

All existing 2.1.x / 2.2.x application code continues to work unchanged.
**To use the new `u3v_get_version()` call**, update the SDK headers together
with the library (C/C++), or use the new Python package.

---

## Supported Platforms

| OS | Architecture | Package |
|---|---|---|
| Windows 10 / 11 | x64 | `u3v-sdk-2.3.0-windows-x64.zip` |
| Ubuntu 22.04+ / Debian 12+ | x64 | `u3v-sdk-2.3.0-linux-x64.tar.gz` |
| Raspberry Pi OS / Ubuntu | ARM64 | `u3v-sdk-2.3.0-linux-arm64.tar.gz` |
| macOS 11+ | Apple Silicon | `u3v-sdk-2.3.0-macos-arm64.zip` |
| macOS 11+ | Intel x64 | `u3v-sdk-2.3.0-macos-x64.zip` |
| Python 3.8+ | Any of the above | `u3v-sdk-2.3.0-python.zip` |

For installation and usage instructions, see the package README or contact
the SDK provider. For a summary of improvements across earlier versions, see
`WHATS_NEW.md`.
