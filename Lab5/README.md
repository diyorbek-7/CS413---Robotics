# Lab 5 – Motor parameter identification

Diyorbek Shokirov, 240046

Identification of `T_m` and `k_e` of both drive motors of the TRIK robot from step responses, following
the Lab 5 README (`05_Motor_parameter_identification`).

| File | Content |
|---|---|
| `motor_identification.qrs` | TRIK Studio visual program: 3 s with the motor off (zero-drift check), then every PWM level from −100 % to +100 % in 5 % steps, 2 s on and 1 s off, every cycle logged as `angle_deg,time_ms,pwm` to one file. Saved for motor M4 / encoder E4; for M3 only the motor port, the encoder and the file name were changed |
| `motor_data/` | `steps5_M4.txt`, `steps5_M3.txt` exactly as the robot wrote them, and `battery.txt` with the voltages read on the display before and after each run |
| `robotics_lab5_240046.ipynb` | Colab notebook: the fits, the plots and the report |

Robot lifted, wheels spinning freely. Left wheel = M4 / E4, right wheel = M3 / E3.

To run the notebook, open it in Google Colab with the `motor_data` folder next to it (upload the folder or
clone this repository); it needs `numpy`, `scipy`, `matplotlib`, `pandas`.
