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
