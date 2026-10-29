# Assignment 1 – Motor parameter identification

Diyorbek Shokirov, 240046 — CSC 413 Robotics, Week 5

| File | Content |
|---|---|
| `motor_steps.qrs` | TRIK Studio program A: 3 s with the motor off (zero-drift check), then PWM −100 … +100 % in 5 % steps, 2 s on and 1 s coasting, every cycle `angle,time,pwm` to one file. Saved for M4 / E4 → `steps5_M4.txt` |
| `motor_signal.qrs` | program B, two experiments in one run: square wave ±60 %, period 1 s, 8 s → 1 s off → chirp ±60 %, 0.2 → 3 Hz, 8 s → 1 s coasting; same logging. Saved for M4 / E4 → `signal_M4.txt` |
| `motor_data/` | raw robot files, not edited, and `battery.txt` with the voltages read on the display before and after each run |
| `robotics_lab5_240046.ipynb` | notebook: model, step fits, k_e and T_m, repeatability, both signals against two models, artifacts, analysis; runs top to bottom on `motor_data/` |

Runs (robot lifted, wheels free; left wheel = M4 / E4, right wheel = M3 / E3). For M3 only the motor port,
the encoder and the file name were changed:

| File | Program | Motor |
|---|---|---|
| `steps5_M4.txt`, `steps5_M4_repeat.txt` | A, twice (repeatability) | M4 |
| `steps5_M3.txt` | A | M3 |
| `signal_M4.txt`, `signal_M3.txt` | B | M4, M3 |
