# S7-1500 Conveyor Control & 3s Jam Timer (TIA Portal V19 + KTP700 HMI)

Conveyor control and delayed jam-detection logic written in **SCL** for a Siemens **SIMATIC S7-1500 (`CPU 1511-1 PN`)**, paired with a **SIMATIC HMI `KTP700 Basic PN`** runtime screen and simulated with **S7-PLCSIM** and **WinCC RT Simulator**.

## Stack
- **Engineering Software:** TIA Portal V19
- **PLC:** SIMATIC S7-1500 (`CPU 1511-1 PN`)
- **HMI:** SIMATIC HMI `KTP700 Basic PN` (`Root screen`)
- **Simulation:** S7-PLCSIM + WinCC Runtime Simulator

## Interface & Logic (`Block_1` / `FB1`)
- **Inputs:** `auto_mode`, `start_cmd`, `stop_cmd`, `sensor_blocked`, `reset_cmd` (`Bool`)
- **Outputs:** `belt_run`, `diverter_active`, `alarm_lamp` (`Bool`)
- **Static:** `run_mem` (`Bool`), `alarm_mem` (`Bool`), `jam_timer` (`TON_TIME`, `PT := T#3S`)
- **Behavior:**
  - `stop_cmd` unlatches `run_mem`; `start_cmd` latches `run_mem` when `NOT alarm_mem`.
  - `jam_timer` counts up to `3s` while `run_mem AND sensor_blocked`; on `jam_timer.Q`, `alarm_mem` latches `TRUE` and `run_mem` drops `FALSE`.
  - `reset_cmd` clears `alarm_mem` only when `NOT sensor_blocked`.
  - `belt_run` runs when `run_mem AND NOT alarm_mem`; `diverter_active` triggers on `auto_mode AND sensor_blocked AND NOT alarm_mem`; `alarm_lamp` mirrors `alarm_mem`.
