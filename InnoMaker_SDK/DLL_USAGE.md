# U3V Camera SDK — Library Usage Guide

**Version:** 2.3.2

This guide covers how to integrate and deploy the U3V Camera library
(`u3v_cam.dll`) in your own Windows application. If you only need the
Python interface, jump to [Python](#5-python-38).

---

## 1. Where the library lives in each package

| Package | Library file | Import library / header |
|---|---|---|
| `u3v-sdk-2.3.2-windows-x64.zip` | `bin/u3v_cam.dll` (+ `bin/libusb-1.0.dll`) | `lib/u3v_cam.lib`, `include/u3v/*.h` |
| `u3v-sdk-2.3.2-python-windows-x64.zip` | `u3v_cam/_libs/windows-x64/u3v_cam.dll` (bundled) | Not required — Python wraps it |

Optional ISP color plugins for demosaic / white-balance / gamma / CCM
ship under `bin/plugins/` (C/C++ package) and
`u3v_cam/_libs/windows-x64/plugins/` (Python package).

---

## 2. C / C++ integration

### Include and link

```c
#include <u3v/u3v_sdk.h>
```

| Compiler | Link |
|---|---|
| MSVC 2022 | `u3v_cam.lib` |

Minimum runtime dependency: **libusb 1.0** — ship the bundled
`libusb-1.0.dll` next to your application executable.

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

### Handling grab results

`u3v_stream_grab()` does not return a usable frame on every call, and an
application loop must be written for that:

| Return value | Meaning | What to do |
|---|---|---|
| `U3V_OK` | Complete frame in the buffer | Use it |
| `U3V_ERR_INCOMPLETE` (-13) | Torn or short frame; `received_size` tells how much arrived | Drop it, grab again |
| `U3V_ERR_PROTOCOL` (-6) | Stream re-aligning after a lost frame | Grab again |
| `U3V_ERR_TIMEOUT` (-5) | Nothing arrived within `timeout_ms` | Normal between triggers. In free-run a camera that stopped sending is restarted by the library by itself (see *Automatic recovery*): grab again |

An incomplete or protocol result is normal for the first grab or two after
`u3v_camera_start()`. Count complete frames, not grab calls, when the
application needs a fixed number of frames. `examples/basic_capture.c` shows
the loop.

Every frame carries the camera's own frame counter in `u3v_buffer_t.block_id`.
It increases by one per image the camera produces and is filled in for an
incomplete frame as well, so a gap in the counter tells the application
exactly which image was lost. In a multi-camera system this is how the images
of the other cameras for that moment are identified and discarded, without
stopping anything.

### Stream configuration

```c
u3v_stream_config_t cfg = { .num_buffers = 4, .timeout_ms = 5000, .transfer_size = 0 };
```

| Field | Meaning | Default |
|---|---|---|
| `num_buffers` | Number of images the library holds for the application while it is busy. A larger value lets the application fall behind the camera for longer (for example during a processing burst) without a lost frame. The library always keeps at least 32 images in flight; a `num_buffers` above 32 extends that. Memory use is about max(`num_buffers`, 32) x image size per camera. | 4 (0 selects the default) |
| `timeout_ms` | How long one `u3v_stream_grab()` waits for data before returning `U3V_ERR_TIMEOUT`. | 5000 (0 selects the default) |
| `transfer_size` | Size of each read request for image data. Leave at 0. | 0 = automatic |

The buffering is on the host; the camera holds no frames. `u3v_camera_flush_stream()`
discards everything received but not yet grabbed. `u3v_camera_stop()` and
`u3v_camera_start()` both do this already, so an application that stops and
starts does not need to call it.

### Automatic recovery

A free-run camera can occasionally stop sending. When nothing arrives for
clearly longer than a frame period, the library stops and restarts acquisition
on that camera by itself. The grab that detected the silence still returns
`U3V_ERR_TIMEOUT`; the grabs that follow deliver frames again, and the
application's own stop/start code is not needed. This is on by default and
never acts in trigger mode or in single/multi-frame acquisition, where a quiet
camera is normal.

```c
u3v_stream_set_auto_restart(stream, 0);              /* disable */
uint32_t n = u3v_stream_get_restart_count(stream);   /* restarts so far */
```

`u3v_camera_reset()` is a different thing: a full reset of the camera, after
which it disconnects from USB, restarts with factory default settings and
re-enumerates after about three seconds. The handle is invalid afterwards;
close it, discover and open the camera again. Use it only for a camera that no
longer responds to commands at all.

### Identifying cameras

`u3v_discover()` reports the serial number, model and manufacturer of every
camera your application has **not yet opened**. A camera that is already open
is still listed, but its identity is read from the camera handle instead:

```c
const u3v_cam_info_t *info = u3v_camera_get_info(cam);
/* info->serial, info->model, info->manufacturer */
```

Recommended pattern for a multi-camera system:

1. Call `u3v_discover()` once, before opening any camera, and record each
   serial number.
2. Open each camera and confirm its identity with `u3v_camera_get_info()`.
3. Refer to cameras by serial number from then on. The device index can change
   with plug order; the serial number does not.

### Changing settings during acquisition

**Exposure and gain can be changed while the camera is streaming.** Call
`u3v_camera_set_exposure()` / `u3v_camera_set_gain()` and keep calling
`u3v_stream_grab()`; the new values apply to the frames that follow. No stop or
restart is needed.

**Trigger mode, trigger source and trigger activation** must be changed with the
camera stopped. The stream and buffer can stay as they are:

```c
u3v_camera_stop(cam);
/* change trigger mode / source / activation here */
u3v_camera_start(cam);
```

In trigger mode `u3v_camera_start()` returns once the camera is ready to act on
triggers; the call takes about 0.15 s. Start issuing trigger pulses after it
has returned. Cameras on earlier firmware need an additional readiness step:
enable it with `u3v_camera_set_armed_start(cam, 1)` (the call then takes about
0.4 s); `U3V_ARMED_START=1` in the environment sets the default for the whole
process.

**In trigger mode, if a camera returns timeouts for triggers that were issued,**
stop and start that camera with the same two calls. The stream and buffer can
stay as they are.

**Keep the grab loop faster than the frame period.** At 1456 x 1088 and 60 frames
per second that is 16.7 ms per frame. Call `u3v_stream_grab()` from a thread that
does nothing else and hands each frame to a queue; run demosaic and any other
processing on other threads. A Debug build of the processing is usually too slow
at this size.

**Settings that change the image size or format** — width, height, offsets and
pixel format — require a stop and restart:

```c
u3v_camera_stop(cam);
u3v_stream_destroy(stream);
u3v_buffer_free(&buf);

/* change width / height / offsets / pixel format here */

u3v_stream_create(&stream, cam, &cfg);      /* after the settings change */

uint32_t size = 0;
u3v_camera_get_payload_size(cam, &size);    /* size for the new settings */
u3v_buffer_alloc(&buf, size);

u3v_camera_start(cam);
```

Create the stream after the settings have been changed, and size the buffer
from `u3v_camera_get_payload_size()` at that point rather than reusing the
previous size.

---

## 3. Runtime deployment

When shipping your compiled application to a target machine, copy the
following files. You do **not** need the SDK's `include/`, `lib/`, or
`examples/` at runtime.

Copy alongside your `.exe`:
```
u3v_cam.dll
libusb-1.0.dll
plugins/            (optional, only if you use the ISP plugins)
```

The target machine also needs the WinUSB driver installed for the camera
(see `WINUSB_DRIVER_INSTALL.md` in the C/C++ package).

---

## 4. Overriding where the library is loaded from

At runtime, the library search order is:

1. Environment variable override (if set)
2. Same directory as the executable
3. For Python: the bundled `_libs/windows-x64/` inside the installed
   package

To force a specific build:

```bat
set U3V_CAM_DLL=C:\path\to\u3v_cam.dll
```

---

## 5. Python (3.8+)

The Python package bundles the Windows native library. No separate C
library install is required.

```bat
pip install u3v-sdk-2.3.2-python-windows-x64.zip
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

`read_frame()` raises `u3v_cam.U3VIncompleteFrame` for a torn or short frame
and `u3v_cam.U3VError` for any other error; `U3VError.code` carries the status
code (`u3v_cam.U3V_ERR_TIMEOUT`, `u3v_cam.U3V_ERR_PROTOCOL`, ...). A loop
that needs a fixed number of frames counts the complete ones:

```python
good = 0
while good < 60:
    try:
        frame = cam.read_frame()
    except u3v_cam.U3VIncompleteFrame:
        continue                      # torn/short frame: drop it
    except u3v_cam.U3VError:
        continue                      # timeout / re-alignment: grab again
    good += 1
```

Iterating the camera (`for frame in cam:`) skips incomplete frames
automatically. `examples/basic_capture.py` shows the full loop.

`Camera(num_buffers=4, timeout_ms=5000)` sets the number of images the
library holds while the application is busy (at least 32 are always in
flight) and the per-call timeout. A camera that stops sending is restarted by
the library by itself (`cam.auto_restart`, on by default; `cam.restart_count`
tells how often it happened). `cam.last_frame_block_id` is the camera's frame
counter; a gap in it identifies a lost image. `cam.armed_start` (default
`False`) enables the additional readiness step of `cam.start()` in trigger
mode that cameras on earlier firmware need.

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
```bat
pip install "u3v_cam[viewer]"    :: PyQt6 + pyqtgraph
```

---

## 6. Binary compatibility

The library preserves ABI within the 2.x line. Application code linked
against 2.1.x or 2.2.x runs against 2.3.2 without recompile; drop the
new `u3v_cam.dll` in place of the old one.

---

## 7. Where to look next

- `RELEASE_NOTES.md` — current version's new features, fixes, and
  supported platforms.
- `WHATS_NEW.md` — summary of improvements across versions.
- `DELIVERY_OVERVIEW.md` — full package inventory and installation
  walkthrough.
- `TRIGGER_USAGE.md` — external hardware trigger guide (pulse width,
  wiring, Arduino UNO code, SDK trigger configuration).
- Each package's `docs/` — inline release notes matching the package
  version.
