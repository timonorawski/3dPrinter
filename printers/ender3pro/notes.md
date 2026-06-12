# Notes and settings

## Modifications

- **Board:** stock 8-bit Creality Melzi (atmega1284p), ~v1.1.3. Has a working serial bootloader —
  Klipper flashed successfully via `make flash` over USB. (Manual `avrdude -c arduino` had thrown
  "not in sync" earlier — likely wrong baud or the klippy service holding the port; `make flash`
  handles baud/reset correctly. No ISP needed.) Future Klipper updates: just re-run `make flash`.
- **Hotend upgrade for higher heat:** Spider hotend with a high-temp NTC thermistor
  (sold as a drop-in for the stock Ender thermistor, rated to ~450°C). Spec believed to be
  100K **β3950** (reasonable certainty; datasheet/order specs still being located).
  Reason for upgrade: stock setup hit thermal shutdown before reaching temp for **PA6-CF**.
- **Firmware:** stock **Creality Marlin v1.1.6.2** (per the LCD menu) — NOT a custom build.
  ⚠ This is the notorious Creality firmware that ships with **thermal runaway protection
  disabled** (fire risk). PA6-CF was never actually reached on it: the "thermal shutdown" hit
  earlier was the stock `MAXTEMP` ceiling (~260–275°C), a separate mechanism from thermal-runaway
  protection. Extruder motor/gearing and bed thermistor are unchanged (stock).
- **Why Klipper is also a safety upgrade:** Klipper enables `verify_heater` thermal-protection
  checks by default, fixing the missing-runaway-protection problem — and `max_temp: 315` is what
  finally enables PA6-CF temps that 1.1.6.2's MAXTEMP blocked.

## Rollback

Not pursuing. Current firmware is stock Creality Marlin 1.1.6.2 (lacks thermal runaway protection —
not worth reverting to), and there's no custom build to preserve. Fallback printers exist (RepRap +
Ender 3 v2 w/ Sprite) if this one goes down. Committing to Klipper.

## Klipper migration — config changes vs stock sample

Base config is the unmodified Creality Ender 3 sample (`klipper/printer-ender3pro.cfg`).
Verified against the Marlin dumps below — geometry, steps/mm, and build volume all match stock,
so the sample is correct as-is **except** for the hotend-related values:

```ini
[extruder]
sensor_type: Generic 3950   # for the β3950 high-temp NTC (sample default is EPCOS; see Stage 2)
max_temp: 315               # raised from sample's 250 for PA6-CF; sensor good to ~450
```

`pullup_resistor` default (4700) is correct for the Creality board — no change.

### Post-flash followup (staged)

**Stage 1 — commission on PETG (in progress):**

- [x] Flashed Klipper via `make flash` (USB); cfg has `Generic 3950` + `max_temp: 315`. Clean MCU connect.
- [x] Endstops verified (`QUERY_ENDSTOPS`); homing X/Y/Z all correct direction; idle temps read ambient.
- [~] `PID_CALIBRATE` extruder running; then bed (slower, 5–10 min); `SAVE_CONFIG` after each.
      ⚠ `SAVE_CONFIG` writes tuned PID to the Pi's autosave block ONLY — pull those values back into
      this repo cfg so it stays source-of-truth (don't let the Pi and repo drift).
- [ ] E-steps check: extrude 100mm at PETG temp, measure; correct `rotation_distance` if not ~95
      (Marlin was 93). Give me the leftover-from-120mm-mark + current rotation_distance for the math.
- [ ] Input shaper — locate **ADXL345** in parts bin, `SHAPER_CALIBRATE` + `SAVE_CONFIG`. Leave
      `max_accel: 3000` as the starting point meanwhile (do NOT downgrade to Marlin's stock 500).
- [ ] First PETG print; dial Z offset live (`SET_GCODE_OFFSET` / babystep in Mainsail).
  - Note: at PETG temps (~235–245°C) the 3950-vs-EPCOS curve difference is only ~1–2°C, so the
    thermistor curve question does NOT block Stage 1.

**Stage 2 — before running PA6-CF (resolve the high-temp thermistor curve):**

- [ ] Validate temp at ~290–300°C (this is where 3950 vs EPCOS actually diverge, ~5–10°C):
      heat and check with IR thermometer or K-type probe.
  - IR caveat: shiny brass/plated metal reads low & erratic — measure off Kapton tape / matte
    high-temp paint, or a blob of extruded filament. A contact thermocouple is more reliable.
  - If reading ≈ setpoint → `Generic 3950` confirmed. If off 5–10°C → try
    `EPCOS 100K B57560G104F` and re-check; one will line up.
- [ ] Re-run `PID_CALIBRATE` at the PA6-CF target — PID is temp-dependent, the PETG tune won't carry.
- [ ] If the order/datasheet turns up, confirm: Marlin `TEMP_SENSOR_0 == 11` → 3950 (use Generic 3950);
      `== 1` → it was run on the stock EPCOS curve.
- [ ] Optional zero-ambiguity path: order a known-spec thermistor (PT1000 / named Semitec) if the
      validation is fuzzy or specs can't be found.

## last good official firmware

Reports **v1.1.6.2** via the LCD menu (the `M115` string below carries no version/date).

```text
Send: M115
Recv: FIRMWARE_NAME:Marlin Creality 3D SOURCE_CODE_URL:github.com/MarlinFirmware/Marlin PROTOCOL_VERSION:1.0 MACHINE_TYPE:Ender-3 Pro EXTRUDER_COUNT:1 UUID:cede2a2f-41a2-4748-9b12-c55c62f367ff
Recv: Cap:EEPROM:1
Recv: Cap:AUTOREPORT_TEMP:1
Recv: Cap:PROGRESS:0
Recv: Cap:PRINT_JOB:1
Recv: Cap:AUTOLEVEL:0
Recv: Cap:Z_PROBE:0
Recv: Cap:LEVELING_DATA:0
Recv: Cap:SOFTWARE_POWER:0
Recv: Cap:TOGGLE_LIGHTS:0
Recv: Cap:CASE_LIGHT_BRIGHTNESS:0
Recv: Cap:EMERGENCY_PARSER:0

Send: M211
Recv: echo:Soft endstops: On   Min: X0.00 Y0.00 Z0.00  Max: X235.00 Y235.00 Z250.00
```

### EEPROM Settings Dump

```text
Send: M503
Recv: echo:  G21    ; Units in mm
Recv: echo:  M149 C ; Units in Celsius
Recv:
Recv: echo:Filament settings: Disabled
Recv: echo:  M200 D1.75
Recv: echo:  M200 D0
Recv: echo:Steps per unit:
Recv: echo:  M92 X80.00 Y80.00 Z400.00 E93.00
Recv: echo:Maximum feedrates (units/s):
Recv: echo:  M203 X500.00 Y500.00 Z5.00 E25.00
Recv: echo:Maximum Acceleration (units/s2):
Recv: echo:  M201 X500 Y500 Z100 E1000
Recv: echo:Acceleration (units/s2): P<print_accel> R<retract_accel> T<travel_accel>
Recv: echo:  M204 P500.00 R500.00 T500.00
Recv: echo:Advanced: S<min_feedrate> T<min_travel_feedrate> B<min_segment_time_ms> X<max_xy_jerk> Z<max_z_jerk> E<max_e_jerk>
Recv: echo:  M205 S0.00 T0.00 B20000 X10.00 Y10.00 Z0.40 E5.00
Recv: echo:Home offset:
Recv: echo:  M206 X0.00 Y0.00 Z0.00
Recv: echo:Material heatup parameters:
Recv: echo:  M145 S0 H185 B45 F255
Recv:   M145 S1 H240 B70 F255
Recv: echo:PID settings:
Recv: echo:  M301 P23.81 I1.93 D73.64
Recv: ok
```
