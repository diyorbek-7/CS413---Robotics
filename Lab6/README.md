# Lab 6 – Localization by odometry

Diyorbek Shokirov, 240046

The robot drives the digits "46" as straight strokes (50 % power) and 90° turns in place (40 %). The odometry
pose is computed from the two encoders every 5 ms and logged; the simulator run and the real run are compared in
the notebook.

| File | Content |
|---|---|
| `trajectory_sim.qrs` | TRIK Studio block program for the 2D simulator → `odometry_data_sim.txt` |
| `trajectory_real.qrs` | the same blocks for the real robot → `odometry_data_real_240046.txt` on the robot (a name of my own, because the robot is shared and classmates' programs write `odometry_data_real.txt`); copied from the robot unchanged and saved here as `odometry_data_real.txt` |
| `odometry_data_sim.txt`, `odometry_data_real.txt` | raw logs, one line per sample: `x,y,time` (m, m, ms), no header |
| `robotics_lab6_240046.ipynb` | notebook: both trajectories, the error over time, the measured end point and the conclusions |

## Simulator and real robot

The two programs have the same blocks and differ only in the robot size and the move times:

| | Simulator | Real robot |
|---|---|---|
| wheel radius `r`, wheel distance `B` | 0.028 m, 0.154 m (2D-model robot) | 0.0375 m, 0.194 m |
| short / long stroke at 50 % | 1480 / 2960 ms (0.2 / 0.4 m) | 680 / 1380 ms |
| left 90° / right 90° / left 180° turn at 40 % | 1120 / 1120 / 2240 ms | 675 / 545 / 1240 ms |

After every move the motors stop for 200 ms. The real times come from two short test runs on the robot. With
the first times (800 / 1600 ms strokes, 720 / 720 / 1440 ms turns, no stops) the left turns came out 104–115°, the
right turns 109–128° and the half turn 223°: the real motors turn faster than the simulated ones, and faster to
the right than to the left. A second test (620 / 1280 ms, 595 / 530 / 1160 ms, with stops) gave 82° / 87° / 168°
and 0.18 m strokes, which set the final values.

## How the program works (16 blocks)

1. **Setup:** clear the encoders, delete the old log, then one Expression with the robot size `r`, `B` and
   the moves as a table: `L[k]`, `R[k]` = power of the left (M3) and right (M4) motor, `T[k]` = time in ms.
2. **Loop over the moves** (`k < n`): set M3 to `L[k]` and M4 to `R[k]`, remember `t0 = time()`, and while
   `time() - t0 < T[k]` run the odometry Expression, write one line `x,y,time` to the file, wait 5 ms.
3. **End:** stop the motors and beep. The last move is 0.5 s with both powers 0, so the final pose is logged.

Odometry (left wheel = E3, right wheel = E4): `path = (E4 + E3)·r/2`, `course = (E4 − E3)·r/B` (angles converted
to radians), `x += Δpath·cos(mean course)`, `y += Δpath·sin(mean course)`, as in the course README.
