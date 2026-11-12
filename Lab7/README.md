# Lab 7 – Go-to-goal

Diyorbek Shokirov, 240046

The robot drives the digits "46" through 11 waypoints with a go-to-goal controller on top of the Lab 6
odometry. The same block program, with the same gains, is made for the TRIK Studio 2D model and for the real
robot.

**Real run:** the shared robot was busy for the whole week, so, as the professor allowed, the real data come
from my Lab 6 run. It is the same "46" with the same corner points and the same odometry, but driven open loop
(timed moves). The Lab 6 log is copied unchanged and the end point is the Lab 6 tape measurement.
`go_to_goal_real.qrs` is ready for the robot but was not run.

| File | Content |
|---|---|
| `go_to_goal_sim.qrs` | block program for the 2D model → `odometry_data_sim.txt` |
| `go_to_goal_real.qrs` | the same program for the real robot (writes `go_to_goal_real_240046.txt`, my own name because the robot is shared); not run, see above |
| `odometry_data_sim.txt` | 2D-model log, one line per loop: `x,y,time` (cm, cm, ms), no header |
| `odometry_data_real.txt` | real robot log from Lab 6, unchanged: `x,y,time` (m, m, ms), converted to cm in the notebook |
| `robotics_lab7_240046.ipynb` | notebook: planned vs model vs real path, waypoint misses, run times, measured end point, conclusions |

## Waypoints (cm)

Start at (0, 0) facing +x, y is to the robot's left.

```
4:    (0,0) → (0,-20) → (20,-20) → (20,0) → (20,-40)
gap:  (20,-40) → (40,-40)
6:    (40,-40) → (40,-20) → (60,-20) → (60,-40) → (40,-40) → (40,0) → (60,0)
```

## Controller

Every loop, as in the lab README:

```
rho = sqrt(dx² + dy²)                       dx, dy = waypoint − position
e   = atan2(dy, dx) − theta,  e = atan2(sin e, cos e)          wrapped to (−π, π]
v   = min(max(Kv·rho, v_min), v_max) · max(1 − |e| / e_drive, 0)
w   = clamp(Kθ·e, −w_max, w_max)
vR, vL = v ± w·B/2,   power = 100 · vR,L / V100   [%]
next waypoint when rho < eps
```

Two changes to the lecture version:

- **`v_min`.** Close to a waypoint `Kv·rho` asks for less than the motor dead zone, so the robot would stop
  short and never get inside `eps`. 10 cm/s is about 17 % power on my robot.
- **Turn, then drive.** There is no forward speed while `|e| > e_drive`. In my first simulator run, with `v`
  scaled by `cos e` as in the course example, the robot drove an arc after every corner (path error up to
  3.6 cm). With this factor it turns on the spot and the strokes stay within 1.5 cm of the digit.

| Gain (both programs) | `Kv` | `v_min` | `v_max` | `Kθ` | `w_max` | `e_drive` | `eps` |
|---|---|---|---|---|---|---|---|
| value | 1.5 1/s | 10 cm/s | 15 cm/s | 4 1/s | 1.5 rad/s | 0.3 rad | 2 cm |

The programs differ only in the robot constants and the log name:

| | 2D model | Real robot |
|---|---|---|
| wheel radius `r` | 2.8 cm | 3.75 cm |
| wheel distance `B` | 15.4 cm | 19.4 cm |
| `V100` = wheel speed at 100 % power | 27 cm/s (550°/s, measured in the 2D model) | 60 cm/s (Lab 5: 16.4 rad/s at 100 %) |

The gains are in cm/s and rad/s and `V100` turns them into motor power, so both robots are asked for the same
speeds. Left wheel = M4/E4, right wheel = M3/E3, as found in Lab 5; the 2D model is set the same way, so both
programs also use the same ports.

## How the program works (18 blocks)

1. **Setup:** clear the encoders, delete the old log, one Expression with `r`, `B`, `V100`, the gains and the
   waypoint tables `X`, `Y`.
2. **Outer loop** over the waypoints (`i < n`); **inner loop** while `rho > eps`: one Expression updates the
   odometry (Lab 6 formulas, in cm) and computes the controller, two Motors blocks set M4 = `PL`, M3 = `PR`,
   one line `x,y,time` is written, Timer 5 ms. When `rho < eps`: `i = i + 1`.
3. **End:** brake, wait 0.5 s, log the pose once more (so the last line is where the robot stopped), beep.

The controller is P only and has no `dt` in it, so the slower loop on the robot (one file write per loop) changes
how often it corrects, not the gains.
