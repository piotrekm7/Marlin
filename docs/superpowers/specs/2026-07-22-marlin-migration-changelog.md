# Changelog migracji: Marlin 2.0.4.3 -> 2.1.3-b3 (Hypercube / SKR PRO v1.1)

Metoda: re-apply na czystej bazie 2.1.3-beta3. Configi wzięte z tagu upstream,
Twoje ustawienia naniesione po jednym. Build: `pio run -e BTT_SKR_PRO` = SUCCESS
(Flash 17.1%, RAM 5.9%).

## Przeniesione ustawienia (70)

### Configuration.h (41)
- Tożsamość/komunikacja: author, SERIAL_PORT 1, SERIAL_PORT_2 -1, BAUDRATE 115200
- Płyta/nazwa: BOARD_BTT_SKR_PRO_V1_1, "Hypercube"
- Drivery: X/Y/Z/E0 = TMC5160
- Kinematyka: COREXY
- Geometria: bed 300x300, Z max 250, X/Y min pos -40
- Kierunki: INVERT_X true, INVERT_Y false, INVERT_Z true
- Steps: { 320, 320, 800, 826 }
- Ruch: accel/retract/travel 1000, S_CURVE_ACCELERATION
- PID hotend: Kp 28.98, Ki 2.31, Kd 90.95
- Termika: TEMP_SENSOR_BED 1, PREHEAT_2_TEMP_BED 100
- Filament: 1.75, EXTRUDE_MAXLENGTH 1100
- Feature: NOZZLE_PARK_FEATURE, INDIVIDUAL_AXIS_HOMING_MENU, SDSUPPORT,
  SD_CHECK_AND_RETRY, REPRAP_DISCOUNT_FULL_GRAPHIC_SMART_CONTROLLER

### Configuration_adv.h (29)
- TMC5160: X/Y/Z/E0 CURRENT 600, MICROSTEPS 32, RSENSE 0.075,
  CHOPPER_TIMING 24V, MONITOR_DRIVER_STATUS, TMC_DEBUG, TMC_USE_SW_SPI
- MICROSTEP_MODES { 32, 32, 16, 16, 16, 16 }
- Sensorless: SENSORLESS_HOMING, X_STALL 0, Y_STALL 2
- Timing (32-bit): MAXIMUM_STEPPER_RATE 5000000, pre/post dir delay 20/20
- Feature: ADVANCED_OK, ADVANCED_PAUSE_FEATURE, BEZIER_CURVE_SUPPORT,
  FWRETRACT, HOST_ACTION_COMMANDS

## Opcje przemianowane 2.0.4.3 -> 2.1.3-b3 (przeniesione na nowe nazwy)

| stara | nowa | wartość |
|---|---|---|
| HOMING_FEEDRATE_XY / _Z | HOMING_FEEDRATE_MM_M | { (50*60), (50*60), (3*60) } |
| XY_PROBE_SPEED | XY_PROBE_FEEDRATE | 2000 |
| X/Y/Z_MIN_ENDSTOP_INVERTING true | X/Y/Z_MIN_ENDSTOP_HIT_STATE | LOW |
| X/Y_HOME_BUMP_MM 0 | HOMING_BUMP_MM (tablica) | { 0, 0, 2 } |
| MINIMUM_STEPPER_PULSE 0 | MINIMUM_STEPPER_PULSE_NS | zostawione domyślne (rate rządzi) |

## Do zweryfikowania na sprzęcie (zmiana semantyki)

- **Endstop HIT_STATE = LOW**: stara opcja `_INVERTING true` przełożona na `HIT_STATE LOW`.
  Mechanicznie to samo zachowanie co miałeś. Przy homingu sensorless kierunek zależy
  od sprzętu (DIAG), więc sprawdź homing X/Y/Z ostrożnie przy pierwszym uruchomieniu.

## Prąd homingu sensorless (nowe w 2.1.x)

- `X_CURRENT_HOME` / `Y_CURRENT_HOME` = **400 mA** (prąd roboczy 600). Obniżony prąd
  przy homingu sensorless daje czystszą detekcję stall i mniej stukania w ramę.
  Z pominięte (nie jest sensorless). 400 to punkt startowy do dostrojenia razem
  ze `X_STALL_SENSITIVITY`/`Y_STALL_SENSITIVITY`.

## Nieistotne zmiany domyślnych (bez akcji)

Kosmetyka: `*_ENABLE_ON 0->LOW` (to samo), `Z_SAFE_HOMING_*_POINT ->X/Y_CENTER`
(to samo), `PID_MAX BANG_MAX->255` (to samo). Bezpieczne: `WATCH_TEMP_PERIOD 20->40 s`,
`PID_FUNCTIONAL_RANGE 10->20`. Reszta driftu dotyczy funkcji których nie używasz
(E1-E7, X2/Y2/Z2+, MMU2, spindle/laser).

## Linear Advance

- `LIN_ADVANCE` włączone, `ADVANCE_K` = **0.45** (w 2.1.x `LIN_ADVANCE_K` -> `ADVANCE_K`).
  K dostrajalne w locie przez `M900 K<wartość>`.

## Firmware
`.pio/build/BTT_SKR_PRO/firmware.bin` -> skopiuj na kartę SD, włóż do SKR PRO,
reset. Nazwa `firmware.bin` jest wymagana przez bootloader BTT.
