# U3V Camera SDK — External Trigger Usage Guide

**Version:** 2.3.2
**Camera in this package:** U3V-CAM-IMX296

This document is applicable to any U3V-compliant camera in the
InnoMaker U3V SDK family. Wiring, host code, and SDK trigger
configuration are identical across sensors. Only the
**sensor-specific reference values** in §1 change per product; look
them up in the corresponding camera's User Manual, Chapter 3.

Common trigger sources covered: Arduino, STM32 / ESP32 / generic
microcontroller, TTL function generator, and 24 V PLC output.

For the electrical and timing spec of the camera in this package, see
the **U3V-CAM-IMX296 User Manual, Chapter 3** (Trigger & Strobe Timing)
and **Chapter 4.4** (Trigger IN Circuit Diagrams).

---

## 1. Sensor-Specific Reference Values

The following values change per camera model. Values below apply
to **U3V-CAM-IMX296**. When integrating another camera in the family
(e.g. IMX585, IMX9281), replace them with the numbers from that
camera's User Manual, Chapter 3.

| Parameter | U3V-CAM-IMX296 | Manual reference |
|---|---|---|
| Full-ROI resolution | 1456 x 1088 | Ch. 1.2 |
| Max frame rate @ full ROI (MONO8) | 60 fps | Ch. 1.2 |
| Frame readout time @ full ROI | ~16.7 ms | Derived from max fps |

### 1.1 Trigger signal characteristics

Per the camera User Manual, Chapter 3:

- **Edge**: the camera module is triggered by the **rising or falling
  edge** of the signal input. Both polarities are supported; select the
  one that matches your source using `TriggerActivation`.
- **Recommended pulse width**: **1 ms or above**.

### 1.2 Trigger period limits

Each trigger produces one frame. The trigger period **T** must satisfy:

```
T >= ExposureTime + FrameReadoutTime
T >= 1 / MaxFrameRate(currentROI)
```

- For U3V-CAM-IMX296 at full ROI: T >= max(Exposure + 16.7 ms, 16.7 ms)
  — max ~60 fps.
- Reduced-height ROIs raise the ceiling proportionally (frame rate is
  inversely proportional to active rows).
- Triggers arriving faster than the sustainable rate are dropped.

---

## 2. Signal Level and Wiring

The `Trig+` / `Trig-` input is opto-isolated and accepts **3.3 V – 24 V**
logic. Wiring by source type:

| Source | Logic level | Series resistor | Notes |
|---|---|---|---|
| Arduino UNO / Nano / Mega | 5 V | Not required | Direct GPIO drive |
| TTL function generator | 5 V | Not required | 50 Ω output OK |
| STM32 / ESP32 / generic MCU / other 3.3 V GPIO | 3.3 V | Not required | Any push-pull GPIO |
| Industrial PLC output | 12 V | ~340 Ω external | See §2.2 |
| Industrial PLC output | 24 V | ~940 Ω (1 kΩ) external | See §2.2 |

**Camera pinout** (Hirose HR10A-7P-6S cable):

| Signal | Camera pin | Cable colour |
|---|---|---|
| Trig+ | Pin 2 | White / Brown |
| Trig- / GND_ISO | Pin 5 | Yellow |

### 2.1 Direct-drive (3.3 V or 5 V logic)

Connect the source GPIO to Pin 2 (Trig+) and its ground to Pin 5
(Trig-). No external components required.

### 2.2 Higher-voltage sources (12 V / 24 V PLC)

For `VCC > 5 V`, add an external series resistor per the formula in
User Manual Chapter 4.4.1:

```
R_add = R_total - R_onboard
      = (VCC - Vf) / I - 200 Ω
```

with `Vf ≈ 1.25 V` and `I ≈ 20 mA`. Round up to the nearest standard
value.

| VCC | R_add (typical) |
|---|---|
| 12 V | 340 Ω (use 330 Ω / 360 Ω) |
| 24 V | 940 Ω (use 1 kΩ) |

---

## 3. Host-Side Trigger Code

### 3.1 Arduino UNO / Nano / Mega (5 V)

Single pulse:
```cpp
const int TRIG_PIN  = 8;
const int PULSE_MS  = 1;    // recommended: 1 ms or above

void setup() {
    pinMode(TRIG_PIN, OUTPUT);
    digitalWrite(TRIG_PIN, LOW);
}

void triggerOnce() {
    digitalWrite(TRIG_PIN, HIGH);
    delay(PULSE_MS);
    digitalWrite(TRIG_PIN, LOW);
}

void loop() {
    triggerOnce();
    delay(100);             // 10 Hz
}
```

Fixed-rate (drift-free scheduling):
```cpp
const int  TRIG_PIN  = 8;
const int  PULSE_MS  = 1;    // recommended: 1 ms or above
const long PERIOD_MS = 20;   // 50 Hz

unsigned long next_ms = 0;

void setup() {
    pinMode(TRIG_PIN, OUTPUT);
    next_ms = millis();
}

void loop() {
    if ((long)(millis() - next_ms) >= 0) {
        digitalWrite(TRIG_PIN, HIGH);
        delay(PULSE_MS);
        digitalWrite(TRIG_PIN, LOW);
        next_ms += PERIOD_MS;
    }
}
```

### 3.2 STM32 (HAL, generic)

```c
#define TRIG_PORT  GPIOA
#define TRIG_PIN   GPIO_PIN_5     /* PA5 push-pull output */

void trigger_once(void) {
    HAL_GPIO_WritePin(TRIG_PORT, TRIG_PIN, GPIO_PIN_SET);
    HAL_Delay(1);                 /* 1 ms pulse (recommended: >= 1 ms) */
    HAL_GPIO_WritePin(TRIG_PORT, TRIG_PIN, GPIO_PIN_RESET);
}
```

For hard-real-time trigger streams, drive the pin from a TIM channel
in PWM one-pulse mode.

### 3.3 TTL function generator

Configure the generator for a **pulse** waveform (not sine):
- Amplitude: 3.3 V or 5 V (both work)
- High-level width: >= 1 ms (recommended)
- Frequency: within the sensor's max frame rate for the current ROI
- Output impedance: 50 Ω (typical); no external termination needed

### 3.4 PLC / 24 V industrial output

Wire the PLC output (`+24 V` HIGH) to `Trig+` through a **1 kΩ series
resistor** (see §2.2). Common the PLC 0 V to `Trig-`. Typical PLC scan
cycles produce 1–10 ms pulses, which meets the >= 1 ms recommendation.

---

## 4. Camera Configuration (SDK Side)

All TriggerXxx settings must be applied **before** `u3v_camera_start()`
/ `cam.start()`. This is host-agnostic — identical for every trigger
source above.

Start the trigger pulses only after `u3v_camera_start()` / `cam.start()` has
returned. In trigger mode the call takes about 0.15 s and returns once the
camera is ready to act on triggers. Cameras on earlier firmware need an
additional readiness step: enable it with `u3v_camera_set_armed_start(cam, 1)`
/ `cam.armed_start = True` (the call then takes about 0.4 s).

### 4.1 C
```c
#include <u3v/u3v_sdk.h>

u3v_camera_open(&cam, 0);
u3v_camera_set_pixel_format(cam, PFNC_MONO8);
u3v_camera_set_exposure(cam, 5000);   /* 5 ms */

/* Named constants from <u3v/u3v_types.h> */
u3v_camera_set_trigger_selector  (cam, U3V_TRIGGER_SELECTOR_FRAME_START);
u3v_camera_set_trigger_source    (cam, U3V_TRIGGER_SOURCE_LINE1);       /* hardware */
u3v_camera_set_trigger_activation(cam, U3V_TRIGGER_ACTIVATION_RISING_EDGE);
u3v_camera_set_trigger_mode      (cam, U3V_TRIGGER_MODE_ON);

/* Read any of these back if needed, e.g.: */
uint32_t activation;
u3v_camera_get_trigger_activation(cam, &activation);

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

### 4.2 Python
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
            frame = cam.read_frame()   # blocks until next external pulse
    finally:
        cam.stop()
        cam.configure_trigger(on=False)   # restore continuous mode
```

---

## 5. Software Trigger Alternative

If no external trigger source is available, the same acquisition model
is reachable through software trigger:

```python
cam.configure_trigger(on=True, source="software",
                      activation="rising", selector="frame_start")
cam.start()
for _ in range(10):
    cam.software_trigger()
    frame = cam.read_frame()
```

Full example: `examples/trigger_mode.py` in the Python package.

---

## 6. Troubleshooting

| Symptom | Check |
|---|---|
| No frame on any trigger | `TriggerMode` was set to On **before** `start()`; `TriggerActivation` matches signal edge; `Trig-` shares ground with source |
| Occasional missed frames | Ensure pulse width >= 1 ms; slow the trigger rate so T >= Exposure + Readout |
| `read_frame()` timeout | Trigger period too short, exposure too long, or wiring disconnected |
| Continuous mode broken after trigger session | Call `configure_trigger(on=False)`, then `stop()` + `start()` again |
| Unstable trigger over long cables | Twisted pair, shielded cable, keep length <= 3 m |
| 24 V PLC: opto not activating | Verify series resistor value; confirm PLC 0 V is tied to `Trig-` |

---

## 7. See Also

- Camera User Manual, Chapter 3 — Trigger & Strobe timing (source of
  truth for the sensor-specific values in §1)
- Camera User Manual, Chapter 4.4 — Trigger IN circuit diagrams
- Python package `examples/trigger_mode.py` — software trigger example
- C/C++ package `examples/multi_frame_capture.c` — MultiFrame acquisition example
- `RELEASE_NOTES.md` — current version and supported platforms
- `DLL_USAGE.md` — library integration and deployment
- `DELIVERY_OVERVIEW.md` — full package inventory and install steps
