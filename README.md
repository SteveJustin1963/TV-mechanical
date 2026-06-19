# Mechanical Television — Nipkow Disk Televisor

## Why: The History

Paul Gottlieb Nipkow patented the scanning disk in 1884 — 40 years before anyone built a working television with it. His insight was elegant: punch a spiral of holes in a spinning disk, and as it rotates each hole traces a horizontal line across a scene. A photocell behind the disk converts the varying light into an electrical signal. At the receiver, the same signal drives a variable light source behind an identical disk spinning in sync — and the image reconstructs itself, line by line, 12.5 times a second.

John Logie Baird demonstrated the world's first working television in 1925 using exactly this principle. His "televisor" used a 30-line Nipkow disk, a neon lamp, and a lens. The BBC broadcast Baird's mechanical TV signal until 1937 when electronic CRT systems took over. Mechanical television lasted roughly 12 years in broadcast — but the physics is permanently beautiful.

**Why build one now?**
- The entire system fits on a table and costs under £30 in parts
- Every subsystem is visible and debuggable with a multimeter
- It teaches scanning, sync, bandwidth, and signal encoding at a fundamental level
- It is genuinely watchable — recognisable faces and movement at 32 lines

---

## How It Works

### The Nipkow Disk

A disk with **32 holes** arranged in a single-turn Archimedean spiral. As the disk spins at **750 RPM** (12.5 fps × 60 seconds), hole 1 sweeps the top scan line, hole 2 the next, and so on down to hole 32. One complete revolution = one complete frame.

Each hole is a tiny aperture — typically **1–2 mm diameter**. The active image area (the raster) is the annular band traced by all holes — typically **16–20 mm tall** on a 100–200 mm disk.

A **sync hole** (or reflective marker) near the centre of the disk triggers an IR sensor once per revolution, locking the electronics to the mechanical position.

### The Signal

The NBTV (Narrow Bandwidth Television) standard:
- **32 lines** per frame
- **12.5 frames per second**
- **3:2 portrait aspect ratio** (taller than wide)
- Scanning direction: bottom-to-top, right-to-left (disk rotates anti-clockwise viewed from front)
- Total bandwidth: ~**13 kHz**
- **31 line sync pulses** per frame; the missing 32nd pulse = frame sync
- Signal is audio-range — can be stored on CD, transmitted on AM/FM, or generated digitally

The video signal amplitude controls the brightness of an LED. Black = LED off, white = LED full brightness. The LED modulates at up to 13 kHz — well within the capability of any fast LED.

### Bandwidth Formula

```
bandwidth = lines × pixels_per_line × frame_rate / 2
           = 32 × 32 × 12.5 / 2  ≈  6.4 kHz  (minimum)
practical  ≈  12–13 kHz (with sync and guard bands)
```

---

## Part 1: Classic Cardboard Build

### Materials List

| Item | Qty | Notes |
|------|-----|-------|
| Black card/cardboard (1–2 mm thick) | 1 sheet A3 | Must be opaque — test with a torch |
| Compass + ruler | — | For laying out the spiral |
| Sharp awl or 1 mm drill bit | — | Punching the holes |
| Small DC motor, 6–12V | 1 | Needs to reach 750 RPM; hobby gearmotor or cassette motor |
| White/warm-white LED (high brightness) | 1 | 5 mm or 3 mm, 1000–5000 mcd minimum |
| 100Ω resistor | 1 | Current limit for LED |
| NPN transistor (e.g. 2N2222, BC548) | 1 | LED driver |
| IR LED + IR phototransistor pair | 1 each | Sync detection |
| 10kΩ resistor | 1 | Pull-up for phototransistor |
| Cardboard box or plywood | — | Housing/frame |
| 9V battery or bench PSU | — | Power |
| Magnifying lens (optional) | 1 | ~50 mm focal length, enlarges image |
| Audio cable / 3.5 mm jack | — | Signal input from phone/CD player |
| Potentiometer 10kΩ | 1 | Brightness/contrast adjust |
| Small louvers or a black tube | — | Light baffling around the viewing aperture |

### Disk Layout — Step by Step

The disk converts a mechanical angle into a scan line. The key equation:

```
For N lines, N holes, disk outer radius R, raster height h:
  hole_n radius  = R - (n / N) × h
  hole_n angle   = n × (360° / N)
```

**Worked example — 200 mm disk, 32 lines, 40 mm raster:**

| Hole | Angle (°) | Radius from centre (mm) |
|------|-----------|------------------------|
| 1 | 0 | 100 |
| 2 | 11.25 | 98.75 |
| 3 | 22.5 | 97.5 |
| ... | ... | ... |
| 32 | 348.75 | 61.25 |

**To draw it manually:**
1. Cut a circle 200 mm diameter from black card
2. Mark the centre
3. Using a protractor, mark 32 radial lines at 11.25° intervals
4. On each radial line, measure the radius from the table above and mark the hole centre
5. Punch or drill 1.5 mm holes at each mark
6. Add one extra small hole at ~40 mm radius (any angle) — this is the sync hole
7. Sand or seal any torn card fibres — rough edges diffuse light

**Disk balance:** If the disk wobbles, tape small coins to the light side until it spins true. An unbalanced disk at 750 RPM will vibrate and blur the image.

### Motor and Drive

- Target speed: **750 RPM** (12.5 fps)
- A cassette player motor, small DC fan motor, or any 6–12V DC motor near this speed range will work
- Mount the motor on a rigid platform; vibration is the enemy
- Attach the disk to the motor shaft with a friction hub (rubber grommet), shaft collar, or a printed/card hub glued to the disk centre
- The disk must run true (no wobble) — check with a pencil held near the edge while spinning

**Motor speed tuning without Arduino:**
- Vary supply voltage (4–12V typical) to adjust RPM
- Use a stroboscope or phone camera at 12.5 fps to check — the disk should appear stationary
- Or count sync pulses per second with a frequency counter/oscilloscope

### Optical Setup

```
[Video signal source] → [LED driver circuit] → [LED] → [Nipkow disk] → [Viewer]
                                                           ↑
                                                    [IR sync sensor]
```

- The LED sits **behind the disk**, centred on the raster band (the annular region swept by the holes)
- Distance from LED to disk: **5–15 mm** — closer = brighter but more bleed-through around holes
- A small black tube or snout around the LED collimates the light and reduces scatter
- A magnifying lens **in front of the disk** enlarges the tiny raster to ~50–80 mm — much more comfortable to view
- The viewing aperture (a hole in the front panel) must be no wider than the raster band; mask off everything else

### LED Driver Circuit

```
                    +9V
                     |
                    [R1 = 100Ω]
                     |
                    LED (anode)
                     |
                    LED (cathode)
                     |
                   Collector
                  [2N2222]
                   Emitter
                     |
                    GND

Signal input ──[R2 = 10kΩ]──Base of 2N2222
                             |
                           [R3 = 1kΩ to GND]
```

- Signal from audio output of phone/laptop (0–1V peak)
- R2 and R3 form a voltage divider / bias network
- Adjust R2 to set gain/contrast — experiment between 4.7kΩ and 22kΩ
- Add a 10µF capacitor in series with the signal input to block DC

### Signal Source

**Option A — Audio file playback (easiest):**
- Download NBTV-encoded video files from [nbtv.media](http://www.nbtv.media) or NBTVA resources
- Play through phone headphone output into the LED driver
- The audio signal IS the video signal — amplitude = brightness

**Option B — CD/cassette player:**
- The Grand Illusions kit encodes video onto a CD audio track
- CD player line output → LED driver → done

**Option C — Software generation:**
- Python or Audacity can generate test signals (vertical bars, grey wedge, face)
- NBTV signal is just amplitude-modulated audio at 0–13 kHz

### Assembly Sequence

1. Build and test LED driver circuit on breadboard first — verify LED responds to audio
2. Mount motor rigidly to baseboard
3. Balance and attach Nipkow disk to motor shaft
4. Mount IR LED and phototransistor either side of the sync hole at ~40 mm radius
5. Mount video LED behind disk, centred on raster band
6. Build front panel with viewing aperture (slot ~20 mm × 80 mm)
7. Mount lens in front of aperture if using one
8. Wire up: motor PSU, LED driver, IR sync circuit
9. Power up motor, observe sync pulses on oscilloscope/LED
10. Feed in test signal, adjust speed until image locks

---

## Testing & Troubleshooting

### Test 1 — Disk Runs True
**Method:** Hold a pencil or marker 2 mm from the disk edge while spinning at speed  
**Pass:** No contact, consistent gap all round  
**Fail:** Wobble → re-balance or re-mount disk on shaft

### Test 2 — Holes Are Aligned
**Method:** Shine a bright torch behind the spinning disk in a dark room. You should see a thin horizontal bright line (the raster) sweeping up-down or as a stable band.  
**Pass:** Clean horizontal band of light  
**Fail:** Diagonal streak → holes not in correct spiral; dots → too slow  

### Test 3 — Sync Pulse
**Method:** Oscilloscope or LED connected to IR phototransistor output  
**Expected:** One clean pulse per revolution = 12.5 Hz  
**Fail — no pulse:** IR pair misaligned; sync hole too small; ambient light swamping sensor  
**Fail — multiple pulses:** Scan holes passing through IR beam — reposition sensor closer to centre  

### Test 4 — LED Response
**Method:** Plug audio cable from phone playing a 1 kHz sine wave into LED driver  
**Expected:** LED visibly flickers at 1 kHz (use phone camera slow-mo to see it)  
**Fail:** No flicker → check transistor orientation, check bias resistors  
**Too dim:** Increase LED current (reduce R1); check LED is high-brightness type  

### Test 5 — Static Image
**Method:** Play NBTV test card signal (available as audio file)  
**Expected:** Stable raster with discernible pattern  
**Image rolls vertically:** Speed not locked to 12.5 fps — adjust motor voltage  
**Image tears horizontally:** Sync hole misaligned with hole 1 — rotate sync hole position  
**Image too dark:** LED not bright enough; lens needed; room not dark enough  
**Image blurred:** Disk wobble; holes too large; motor vibration  

### Test 6 — Moving Video
**Method:** Play face/motion NBTV clip  
**Expected:** Recognisable human face, visible motion  
**Checklist if poor:** Dark room, magnifying lens, balanced disk, stable motor speed  

### Common Issues and Fixes

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Rolling picture | Motor speed wrong | Adjust voltage; target 750 RPM |
| Picture tears every frame | Sync hole offset | Rotate disk 11.25° increments on shaft |
| Faint / barely visible image | LED too dim | Higher mcd LED; reduce series R; add lens |
| Noisy grainy image | Motor vibration | Decouple motor with foam/rubber mount |
| Picture flips upside down | Disk rotates wrong direction | Swap motor polarity |
| Horizontal white streaks | Scan holes catching torch light | Baffle LED more; dark room |
| No image at all | Signal not reaching LED | Test driver with 1 kHz audio first |
| Speed drifts | Motor load varies | Add flywheel mass; use regulated PSU |

---

## Part 2: Arduino-Controlled Televisor

The classic build works, but has two limitations: motor speed drifts with load/temperature, and you need a pre-encoded audio signal. Arduino solves both.

### What Arduino Adds

1. **Closed-loop speed control** — PLL-style loop reads sync pulses, adjusts PWM to motor
2. **On-board signal generation** — display images/video from SD card or serial
3. **Serial/USB video input** — send frames from PC in real time
4. **Automatic sync** — disk position known at all times; no mechanical sync hole needed (position inferred from timing)
5. **DAC output for LED** — 8-bit brightness resolution instead of raw audio

### Hardware for Arduino Version

| Item | Qty | Notes |
|------|-----|-------|
| Arduino Uno or Mega | 1 | Mega preferred — more timers, more RAM |
| L298N or L9110 motor driver | 1 | For PWM motor control (up to 2A) |
| IR photointerrupter (TCRT5000 or H21A1) | 1 | Sync detection |
| High-brightness white LED | 1 | Same as Part 1 |
| MOSFET (e.g. IRLZ44N) or TIP120 | 1 | LED driver — PWM capable |
| MicroSD card module | 1 | Video storage |
| 100Ω resistor | 1 | LED current limit |
| 10kΩ resistor | 1 | IR sensor pull-up |
| 0.1µF capacitor | 2 | Motor noise decoupling |
| Diode 1N4007 | 1 | Motor flyback protection |
| 12V PSU (or 9V) | 1 | Motor supply |
| 5V regulator or USB | — | Arduino supply |

### Wiring Diagram

```
MOTOR CONTROL:
  Arduino Pin 9 (PWM) ─── L298N ENA ─── Motor + 
  GND ─────────────────── L298N GND
  12V ─────────────────── L298N VCC
  IN1 = HIGH, IN2 = LOW (direction)

SYNC SENSOR:
  IR LED anode ──── 220Ω ──── 5V
  IR LED cathode ── GND
  Phototransistor collector ── 10kΩ ── 5V ── Arduino Pin 2 (INT0)
  Phototransistor emitter ──── GND

LED DRIVER:
  Arduino Pin 6 (PWM) ── 1kΩ ── Gate of IRLZ44N
  IRLZ44N Source ─────────── GND
  IRLZ44N Drain ──────────── LED cathode
  LED anode ──────────────── 100Ω ──── 5V
  (or 12V with higher R for brighter output)
```

### Arduino Firmware — Speed Control Loop

The core of the Arduino firmware is a **PLL (Phase-Locked Loop)** implemented in software:

```cpp
// Nipkow Disk Speed Controller — Arduino Uno/Mega
// Target: 750 RPM = 12.5 RPS = 80ms per revolution

#define MOTOR_PWM_PIN   9
#define SYNC_PIN        2
#define LED_PIN         6

volatile unsigned long lastSyncTime = 0;
volatile unsigned long syncPeriod = 0;
volatile bool newSync = false;

const unsigned long TARGET_PERIOD_US = 80000; // 80ms = 12.5 Hz
int motorPWM = 128;

void syncISR() {
  unsigned long now = micros();
  syncPeriod = now - lastSyncTime;
  lastSyncTime = now;
  newSync = true;
}

void setup() {
  pinMode(MOTOR_PWM_PIN, OUTPUT);
  pinMode(SYNC_PIN, INPUT_PULLUP);
  pinMode(LED_PIN, OUTPUT);
  attachInterrupt(digitalPinToInterrupt(SYNC_PIN), syncISR, FALLING);
  analogWrite(MOTOR_PWM_PIN, motorPWM);
  Serial.begin(115200);
}

void loop() {
  if (newSync) {
    newSync = false;
    long error = (long)TARGET_PERIOD_US - (long)syncPeriod;
    // Proportional control
    int correction = error / 200;
    motorPWM = constrain(motorPWM - correction, 40, 255);
    analogWrite(MOTOR_PWM_PIN, motorPWM);
    
    Serial.print("Period: ");
    Serial.print(syncPeriod);
    Serial.print(" us  PWM: ");
    Serial.println(motorPWM);
  }
}
```

**Tuning:** Increase the divisor (200) to make the loop slower/smoother; decrease for faster response. Add integral term if steady-state error persists.

### Arduino Firmware — SD Card Video Playback

```cpp
// Nipkow Televisor — SD Card Playback
// Image format: 32 bytes per line, 32 lines per frame = 1024 bytes/frame
// Each byte = pixel brightness 0–255

#include <SPI.h>
#include <SD.h>

#define SD_CS_PIN    10
#define LED_PIN       6
#define SYNC_PIN      2
#define MOTOR_PWM    9

// Timing for 32 lines × 12.5fps = 400 lines/sec
// Each line = 2500 µs
// Each pixel = 2500 / 32 = 78 µs

const int LINES = 32;
const int PIXELS = 32;
const unsigned long LINE_US = 2500;
const unsigned long PIXEL_US = LINE_US / PIXELS;  // 78µs

volatile bool frameStart = false;
volatile unsigned long syncTime = 0;

void syncISR() {
  frameStart = true;
  syncTime = micros();
}

void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(MOTOR_PWM, OUTPUT);
  pinMode(SYNC_PIN, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(SYNC_PIN), syncISR, FALLING);
  analogWrite(MOTOR_PWM, 150);
  SD.begin(SD_CS_PIN);
}

File videoFile;
uint8_t frameBuf[LINES * PIXELS];

void displayFrame() {
  // Wait for sync pulse (disk position 0)
  unsigned long deadline = syncTime + 80000UL; // 80ms max
  while (!frameStart && micros() < deadline);
  if (!frameStart) return;
  frameStart = false;
  
  unsigned long t0 = syncTime;
  for (int line = 0; line < LINES; line++) {
    for (int px = 0; px < PIXELS; px++) {
      // Wait for correct pixel time
      while (micros() - t0 < (unsigned long)(line * LINE_US + px * PIXEL_US));
      analogWrite(LED_PIN, frameBuf[line * PIXELS + px]);
    }
  }
  analogWrite(LED_PIN, 0); // blank between frames
}

void loop() {
  if (!videoFile) {
    videoFile = SD.open("video.nbt");
  }
  if (videoFile) {
    if (videoFile.read(frameBuf, sizeof(frameBuf)) == sizeof(frameBuf)) {
      displayFrame();
    } else {
      videoFile.seek(0); // loop
    }
  }
}
```

### Converting Video for SD Card

Use Python on the PC to convert any video to the NBTV format:

```python
import cv2, numpy as np, struct

cap = cv2.VideoCapture("input.mp4")
out = open("video.nbt", "wb")

LINES, PIXELS = 32, 32

while True:
    ret, frame = cap.read()
    if not ret:
        break
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    resized = cv2.resize(gray, (PIXELS, LINES))
    # NBTV scans bottom-to-top, right-to-left
    resized = np.fliplr(np.flipud(resized))
    out.write(resized.tobytes())

cap.release()
out.close()
print("Done")
```

Run at source frame rate; the Arduino loops the file so a 5-second clip will loop continuously.

### Serial Real-Time Video (PC → Arduino)

For live or dynamic content, stream frames over USB Serial:

**Python sender (PC):**
```python
import serial, cv2, numpy as np, time

ser = serial.Serial('/dev/ttyUSB0', 115200)
cap = cv2.VideoCapture(0)  # webcam

while True:
    ret, frame = cap.read()
    if not ret: continue
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    small = cv2.resize(gray, (32, 32))
    small = np.fliplr(np.flipud(small))
    ser.write(b'\xFF')        # frame start marker
    ser.write(small.tobytes()) # 1024 bytes
    time.sleep(1/12.5)
```

**Arduino receiver addition** (add to loop):
```cpp
if (Serial.available() >= 1025) {
  if (Serial.read() == 0xFF) {
    Serial.readBytes((char*)frameBuf, 1024);
    displayFrame();
  }
}
```

### Closed-Loop Motor Speed with Tachometer Display

Add a 16×2 LCD and display live RPM/period for tuning:

```cpp
#include <LiquidCrystal.h>
LiquidCrystal lcd(7, 8, 4, 5, 3, A0);

// In loop(), after speed correction:
float fps = 1000000.0 / syncPeriod;
float rpm = fps * 60.0;
lcd.setCursor(0, 0);
lcd.print("RPM: ");
lcd.print(rpm, 1);
lcd.setCursor(0, 1);
lcd.print("FPS: ");
lcd.print(fps, 2);
```

---

## Calibration and Optimisation

### Finding the Right Motor Speed Without Oscilloscope

1. Play a 12.5 Hz audio tone (generated in Audacity) through a speaker
2. Watch the disk — it should appear to freeze or strobe at the correct speed
3. Alternatively: under fluorescent lighting (which flickers at 50/60 Hz), the disk will appear to have stable "ghost" images at certain speeds — use 12.5 Hz (= 50/4)

### Image Quality Checklist

- [ ] Room as dark as possible
- [ ] LED aimed squarely through raster band
- [ ] Magnifying lens aligned with raster centre
- [ ] Disk balanced — zero wobble
- [ ] Motor on vibration-damping mount (foam pad)
- [ ] Viewing distance: 20–40 cm from lens
- [ ] Brightness: LED at maximum safe current (check datasheet)
- [ ] Contrast: adjust driver gain to use full black-to-white range

### Measured Performance Targets

| Parameter | Minimum | Good | Excellent |
|-----------|---------|------|-----------|
| Frame rate | 10 fps | 12.5 fps | 12.5 fps stable ±0.1% |
| Lines | 16 | 32 | 32 |
| Image size (at lens) | 20 mm | 50 mm | 80 mm |
| Pixel brightness | 50 cd/m² | 200 cd/m² | 500 cd/m² |
| Speed stability (without Arduino) | ±5% | ±2% | — |
| Speed stability (with Arduino PLL) | — | ±0.5% | ±0.1% |

---

## References and Further Reading

- **Instructables — Cardboard Televisor with Arduino:** https://www.instructables.com/A-cardboard-televisor/
- **Grand Illusions Televisor Kit:** https://www.grand-illusions.com/products/televisor-kit
- **Arduino Project Hub — 32 Line Nipkow:** https://projecthub.arduino.cc/christopheArduino/nipkow-disk-32-line-television-6615b2
- **NBTV Beginner's Build Guide:** http://www.nbtv.wyenet.co.uk/beginners.htm
- **LabGuy's World — Big 32-Line Televisor:** https://labguysworld.com/NBTV_BigTelevisor_001.htm
- **NBTV Signal ID Wiki:** https://www.sigidwiki.com/wiki/Narrow-Bandwidth_Television_(NBTV)
- **Analog TV Mechanical Lab Simulator:** https://analogtv.net/mechanical-lab
- **NBTV Web Player (test your signal files):** https://nbtv-player.berrry.app/
- **Hackaday Nipkow tag:** https://hackaday.com/tag/nipkow-disk/
- **Narrow-Bandwidth Television Association:** https://www.taswegian.com/NBTV/
- **Nipkow disk — Wikipedia:** https://en.wikipedia.org/wiki/Nipkow_disk
