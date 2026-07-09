# U3V-CAM-IMX296 — External Trigger Usage Guide

**Version:** 2.2.2
**Example host:** Arduino UNO driving Trig+ / +5 V

This guide covers how to drive the hardware trigger input with a 5 V
logic source (Arduino UNO used as the reference) and how to configure
the camera on the SDK side to accept it.

For the definitive electrical and timing specification, see the
**U3V-CAM-IMX296 User Manual, Chapter 3** (Trigger & Strobe Timing)
and **Chapter 4.4** (Trigger IN Circuit Diagrams).

---

## 1. Minimum Trigger Pulse Width

The camera's hardware trigger input requires a minimum pulse width of:

> **tH ≥ 14.815 µs**

(per User Manual, Chapter 3.1)

Pulses shorter than tH are not guaranteed to be recognized.

Recommended pulse widths when driving from Arduino UNO:

| Priority | Pulse width | When to use |
|---|---|---|
| Minimum | 15 µs | At spec, no margin |
| Recommended | 50 µs | Comfortable margin, tolerant of wiring jitter |
| Conservative | 100 µs | Long cables or noisy environments |

---

## 2. Trigger Period Limits

Each trigger produces one frame. The trigger period **T** must satisfy:

```
T >= ExposureTime + FrameReadoutTime
T >= 1 / MaxFrameRate(currentROI)
```

- At full resolution 1456 x 1088 MONO8, the sustained rate is 60 fps
  (~16.7 ms per frame).
- Reduced-height ROIs raise the ceiling proportionally (frame rate is
  inversely proportional to active rows).
- Triggers arriving faster than the sustainable rate are dropped.

---

## 3. Wiring (Arduino UNO to Camera)

| Signal | Arduino UNO | Camera side |
|---|---|---|
| Trigger signal | Digital output pin (D2..D13) | Pin 2 — Trig+ (White/Brown) |
| Common ground  | GND | Pin 5 — Trig-/GND_ISO (Yellow) |

- Camera trigger input is opto-isolated, 5–24 V (User Manual 4.4).
- Arduino UNO HIGH is approximately 5.0 V — inside spec.
- Arduino GPIO can drive the opto-input directly (no external resistor
  required for the 5 V configuration).

---

## 4. Arduino UNO Code Examples

### 4.1 Single-pulse trigger
```cpp
const int  TRIG_PIN = 8;
const long PULSE_US = 50;   // >= 15 us required, 50 us recommended

void setup() {
    pinMode(TRIG_PIN, OUTPUT);
    digitalWrite(TRIG_PIN, LOW);
}

void triggerOnce() {
    digitalWrite(TRIG_PIN, HIGH);
    delayMicroseconds(PULSE_US);
    digitalWrite(TRIG_PIN, LOW);
}

void loop() {
    triggerOnce();
    delay(100);              // 10 Hz
}
```

### 4.2 Fixed-rate trigger (drift-free scheduling)
```cpp
const int  TRIG_PIN  = 8;
const long PULSE_US  = 50;
const long PERIOD_MS = 20;   // 50 Hz

unsigned long next_us = 0;

void setup() {
    pinMode(TRIG_PIN, OUTPUT);
    next_us = micros();
}

void loop() {
    if ((long)(micros() - next_us) >= 0) {
        digitalWrite(TRIG_PIN, HIGH);
        delayMicroseconds(PULSE_US);
        digitalWrite(TRIG_PIN, LOW);
        next_us += PERIOD_MS * 1000UL;
    }
}
```

### 4.3 Burst trigger (pairs with SDK MultiFrame mode)
```cpp
const int  TRIG_PIN = 8;
const int  BURST    = 5;
const long PULSE_US = 50;
const long GAP_MS   = 20;    // >= Exposure + Readout

void burst() {
    for (int i = 0; i < BURST; i++) {
        digitalWrite(TRIG_PIN, HIGH);
        delayMicroseconds(PULSE_US);
        digitalWrite(TRIG_PIN, LOW);
        delay(GAP_MS);
    }
}
```

---

## 5. Camera Configuration (SDK Side)

All TriggerXxx settings must be applied **before** `u3v_camera_start()`
/ `cam.start()`.

### 5.1 C
```c
#include <u3v/u3v_sdk.h>

u3v_camera_open(&cam, 0);
u3v_camera_set_pixel_format(cam, PFNC_MONO8);
u3v_camera_set_exposure(cam, 5000);   /* 5 ms */

/* GenICam enum values as documented in User Manual, Chapter 3 */
u3v_camera_set_trigger_selector  (cam, 2);  /* FrameStart       */
u3v_camera_set_trigger_source    (cam, 1);  /* Line1 (hardware) */
u3v_camera_set_trigger_activation(cam, 3);  /* RisingEdge       */
u3v_camera_set_trigger_mode      (cam, U3V_TRIGGER_MODE_ON);

u3v_stream_config_t cfg = { .num_buffers = 4, .timeout_ms = 5000 };
u3v_stream_create(&stream, cam, &cfg);
u3v_camera_start(cam);

u3v_buffer_t buf;
uint32_t payload;
u3v_camera_get_payload_size(cam, &payload);
u3v_buffer_alloc(&buf, payload);

while (running) {
    if (u3v_stream_grab(stream, &buf) == U3V_OK) {
        /* buf.data / buf.width / buf.height */
    }
}
```

### 5.2 Python
```python
import u3v_cam

with u3v_cam.Camera() as cam:
    cam.exposure_us = 5000
    cam.gain        = 0

    cam.configure_trigger(
        on=True,
        source="line1",        # hardware trigger input
        activation="rising",   # or "falling"
        selector="frame_start",
    )
    cam.start()
    try:
        while True:
            frame = cam.read_frame()   # blocks until next Arduino pulse
    finally:
        cam.stop()
        cam.configure_trigger(on=False)   # restore continuous mode
```

---

## 6. Software Trigger Alternative

If a Trig+ signal source is not available, the same acquisition model
is reachable through software trigger:

```python
cam.configure_trigger(on=True, source="software",
                      activation="rising", selector="frame_start")
cam.start()
for _ in range(10):
    cam.software_trigger()
    frame = cam.read_frame()
```

Full example: `python/examples/trigger_mode.py`.

---

## 7. Troubleshooting

| Symptom | Check |
|---|---|
| No frame on any trigger | `TriggerMode` was set to On **before** `start()`; `TriggerActivation` matches signal edge |
| Occasional missed frames | Increase pulse width (try 100 us); slow the trigger rate so T >= Exposure + Readout |
| `read_frame()` timeout | Trigger period too short, exposure too long, or wiring disconnected |
| Continuous mode broken after trigger session | Call `configure_trigger(on=False)`, then `stop()` + `start()` again |
| Unstable trigger over long cables | Twisted pair, shielded cable, keep length <= 3 m |

---

## 8. See Also

- U3V-CAM-IMX296 User Manual, Chapter 3 — Trigger & Strobe timing
  (source of truth)
- U3V-CAM-IMX296 User Manual, Chapter 4.4 — Trigger IN circuit diagrams
- SDK `python/examples/trigger_mode.py` — software trigger example
- SDK `examples/multi_frame_capture.c` — MultiFrame acquisition example
- `RELEASE_NOTES.md` — supported features per platform
- `DLL_USAGE.md` — library integration and deployment
- `DELIVERY_OVERVIEW.md` — full package inventory and install steps
