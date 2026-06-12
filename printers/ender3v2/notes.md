# Description/Specs
Ender 3 V2 - acquired used
Board: 4.2.2
Current Firmware: Ender3V2-422-BLTUBL-MPC-20260106.bin
 - https://github.com/mriscoc/Ender3V2S1

## Modifications
- hotend: Sprite
- bltouch

## Calibration Settings (last known good)

BLTouch:
Probe Z Offset: -5.75
X
Y

Extruder: esteps: 

### Potential tweaks (from someone else's config, to verify still)

`
X bed size: 215
Y bed size: 220
X min pos: 0
Y min pos: -5
X max pos: 215
Y max pos: 220
Z max pos: 235

Probe X Offset: -31.4
Probe Y Offset: -40.1
Probe Z Offset: -2.76

Mesh Inset:
Mesh X Min: 10
Mesh X Max: 183.6
Mesh Y Min: 10
Mesh Y Max: 179.9
`

## Start GCode additions:
`
G28 ; Home
M420 L0 S1 T10 ;Load Mesh from Slot 0 and make bed autolevelling active, taper 10
;alternative: G29 ; run auto bed levelling - but this adds time and wear, likely unneeded
M402 ;Retract Probe
`


## Klipper -- first time after switching:

from: https://www.klipper3d.org/Config_checks.html?h=pid

Klipper supports PID control for the extruder and bed heaters. In order to use this control mechanism, it is necessary to calibrate the PID settings on each printer (PID settings found in other firmwares or in the example configuration files often work poorly).

To calibrate the extruder, navigate to the command console and run the PID_CALIBRATE command. For example: PID_CALIBRATE HEATER=extruder TARGET=170

At the completion of the tuning test run SAVE_CONFIG to update the printer.cfg file the new PID settings.

If the printer has a heated bed and it supports being driven by PWM (Pulse Width Modulation) then it is recommended to use PID control for the bed. (When the bed heater is controlled using the PID algorithm it may turn on and off ten times a second, which may not be suitable for heaters using a mechanical switch.) A typical bed PID calibration command is: PID_CALIBRATE HEATER=heater_bed TARGET=60


### Marlin Config Dump
`
Send: M503
Recv: echo:; Linear Units:
Recv: echo:  G21 ; (mm)
Recv: echo:; Auto Temp Control:
Recv: echo:  M104 S0 B250 F0.00
Recv: echo:; Temperature Units:
Recv: echo:  M149 C ; Units in Celsius
Recv: echo:; Filament settings (Disabled):
Recv: echo:  M200 S0 D1.75
Recv: echo:; Steps per unit:
Recv: echo:  M92 X80.00 Y80.00 Z400.00 E424.90
Recv: echo:; Max feedrates (units/s):
Recv: echo:  M203 X500.00 Y500.00 Z10.00 E45.00
Recv: echo:; Max Acceleration (units/s2):
Recv: echo:  M201 X500.00 Y500.00 Z100.00 E1000.00
Recv: echo:; Acceleration (units/s2) (P<print-accel> R<retract-accel> T<travel-accel>):
Recv: echo:  M204 P500.00 R800.00 T1000.00
Recv: echo:; Advanced (B<min_segment_time_us> S<min_feedrate> T<min_travel_feedrate> J<junc_dev>):
Recv: echo:  M205 B20000.00 S0.00 T0.00 J0.10
Recv: echo:; Unified Bed Leveling:
Recv: echo:  M420 S0 Z10.00 ; Leveling OFF
Recv: 
Recv: Unified Bed Leveling System v1.01 inactive
Recv: echo:Active Mesh Slot 0
Recv: echo:EEPROM can hold 7 meshes.
Recv: 
Recv: echo:; BLTouch HS mode:
Recv: echo:  M401 S1 ; ON
Recv: echo:; Material heatup parameters:
Recv: echo:  M145 S0 H195.00 B60.00 F128
Recv: echo:  M145 S1 H240.00 B90.00 F128
Recv: echo:  M145 S2 H230.00 B80.00 F128
Recv: echo:  M145 S3 H190.00 B50.00 F128
Recv: echo:; Bed PID:
Recv: echo:  M304 P462.10 I85.47 D624.59
Recv: echo:; Display Sleep:
Recv: echo:  M255 S5 ; (minutes)
Recv: echo:; LCD Brightness:
Recv: echo:  M256 B127
Recv: echo:; Power-loss recovery:
Recv: echo:  M413 S0 ; OFF
Recv: echo:; Retract (S<length> F<feedrate> Z<lift>):
Recv: echo:  M207 S1.00 W13.00 F2400.00 Z0.20
Recv: echo:; Recover (S<length> F<feedrate>):
Recv: echo:  M208 S0.00 W0.00 F2400.00 R480.00
Recv: echo:; Homing Feedrate:
Recv: echo:  M210 X3000.00 Y3000.00 Z600.00
Recv: echo:; Z-Probe Offset:
Recv: echo:  M851 X-31.50 Y-55.90 Z-3.94 ; (mm)
Recv: echo:; Hotend Idle Timeout:
Recv: echo:  M86 B0 E0 S600 T170
Recv: echo:; Filament load/unload:
Recv: echo:  M603 L0.00 U100.00 ; (mm)
Recv: echo:; Filament runout sensor:
Recv: echo:  M412 S0 D25.00 ; Sensor OFF
Recv: echo:; Model predictive control:
Recv: echo:  M306 E0 P40.00 C14.40 R0.2187 A0.1257 F0.1315 H0.0056
Recv: echo:; Physical minimums:
Recv: echo:  C100 X0 Y0
Recv: echo:; Physical maximums:
Recv: echo:  C101 X230 Y231 Z250
Recv: echo:; Bed size:
Recv: echo:  C102 X230 Y230
Recv: echo:; Mesh inset and leveling settings:
Recv: echo:  C29 L25 R175 F25 B175 X8 Y8 T50 V1 ; 8x8 T=50 C
Recv: echo:; Max Extruder temperature:
Recv: echo:  C104 T275
Recv: echo:; Park Head:
Recv: echo:  C125 X230 Y221 Z20
Recv: echo:; Filament runout sensor:
Recv: echo:  C412 M0 ; Active mode: LOW
Recv: echo:; Invert Extruder:
Recv: echo:  C562 E0 ; No inverted
Recv: echo:; Probe Z Fix, Speed and Multiple Probing:
Recv: echo:  C851 F0.00000 S480 M0
Recv: ok P15 B15

Send: M115
Recv: FIRMWARE_NAME:Marlin 2.1.4 MRiscoC Ender3V2-422-BLTUBL-MPC, based on bugfix-2.1.x (Jan  6 2026 01:58:55) SOURCE_CODE_URL:github.com/mriscoc/Ender3V2S1 PROTOCOL_VERSION:1.0 MACHINE_TYPE:Ender3V2-422-BLTUBL-MPC KINEMATICS:Cartesian EXTRUDER_COUNT:1 UUID:cede2a2f-41a2-4748-9b12-c55c62f367ff
Recv: Cap:SERIAL_XON_XOFF:0
Recv: Cap:BINARY_FILE_TRANSFER:1
Recv: Cap:EEPROM:1
Recv: Cap:VOLUMETRIC:1
Recv: Cap:AUTOREPORT_POS:0
Recv: Cap:AUTOREPORT_TEMP:1
Recv: Cap:PROGRESS:0
Recv: Cap:PRINT_JOB:1
Recv: Cap:AUTOLEVEL:1
Recv: Cap:RUNOUT:1
Recv: Cap:Z_PROBE:1
Recv: Cap:LEVELING_DATA:1
Recv: Cap:BUILD_PERCENT:1
Recv: Cap:SOFTWARE_POWER:0
Recv: Cap:TOGGLE_LIGHTS:0
Recv: Cap:CASE_LIGHT_BRIGHTNESS:0
Recv: Cap:EMERGENCY_PARSER:1
Recv: Cap:HOST_ACTION_COMMANDS:1
Recv: Cap:PROMPT_SUPPORT:1
Recv: Cap:SDCARD:1
Recv: Cap:MULTI_VOLUME:0
Recv: Cap:REPEAT:0
Recv: Cap:SD_WRITE:1
Recv: Cap:AUTOREPORT_SD_STATUS:0
Recv: Cap:LONG_FILENAME:1
Recv: Cap:LFN_WRITE:1
Recv: Cap:CUSTOM_FIRMWARE_UPLOAD:1
Recv: Cap:EXTENDED_M20:1
Recv: Cap:THERMAL_PROTECTION:1
Recv: Cap:MOTION_MODES:0
Recv: Cap:ARCS:1
Recv: Cap:BABYSTEPPING:1
Recv: Cap:EP_BABYSTEP:0
Recv: Cap:CHAMBER_TEMPERATURE:0
Recv: Cap:COOLER_TEMPERATURE:0
Recv: Cap:MEATPACK:0
Recv: Cap:CONFIG_EXPORT:0
Recv: Cap:PROUI_AML:1
Recv: area:{full:{min:{x:0.00,y:0.00,z:0.00},max:{x:230.00,y:231.00,z:250.00}},work:{min:{x:0.00,y:0.00,z:0.00},max:{x:230.00,y:230.00,z:250.00}}}
`