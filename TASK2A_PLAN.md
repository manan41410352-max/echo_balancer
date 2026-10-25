# Echo Balancer — Task 2A working plan

## Goal and scoring

Task 2A has two 30-mark evaluations: PID and LQR, for 60 marks total. Each run lasts up to 30 seconds. Balanced time has 70% weight and precision has 30%. The bot must stay within 10° of upright and within 30° of its initial heading; runs stop early past 45° tilt or 90° yaw.

## State and model

The required state order is:

```text
x = [tilt_rate, tilt, yaw_rate, yaw]
```

The modelling instructions define `α = yaw`, `θ = tilt`, and a torque-input rotary-pendulum model. With

```text
J0 = Jr + mp Lr²
J1 = Jp + mp lp²
Δ  = J0 J1 − (mp Lr lp)²
```

the continuous-time matrices for **torque input** are:

```text
A = [[0,  J0 mp g lp / Δ,             0, 0],
     [1,  0,                           0, 0],
     [0, −mp² g Lr lp² / Δ,            0, 0],
     [0,  0,                           1, 0]]

B_torque = [−mp Lr lp / Δ, 0, J1 / Δ, 0]ᵀ
```

Checks: A is 4×4; B is 4×1; rows 2 and 4 of A are `[1,0,0,0]` and `[0,0,1,0]`; rows 2 and 4 of B are zero; the final column of A is zero; the nonzero signs are `A[1,2] > 0`, `A[3,2] < 0`, `B[1] < 0`, and `B[3] > 0` (1-based indices). The A eigenvalues should be `0, 0, +λ, −λ`, where `λ = sqrt(J0 mp g lp / Δ)`, and the controllability matrix should have rank 4.

**Input-unit caveat:** The scene drives the front motor in velocity-control mode, and the LQR tuner labels its input as motor velocity. The derivation above defines B for torque. Do not enter `B_torque` as though it were already the motor-velocity input; determine the conversion or identify the velocity-input model first.

## Tuning sequence

1. PID: begin with all six gains at zero. Find the correct motor sign, tune tilt Kp then tilt Kd, then tune yaw Kp/Kd. Keep Ki at zero unless a steady residual error remains.
2. LQR: enter continuous-time A/B in the required state order. Begin with Q = I and R = 1, then adjust state weights and control effort for a stable, low-wobble response.
3. Evaluate each controller only after tuning. Save the six PID gains and motor sign; for LQR, save A, B, Q, R, and motor sign.

## Submission checklist

The submission zip must contain a `Task2A/` directory with only the unedited evaluator files:

```text
Task2A/task2a_pid_output.txt
Task2A/task2a_lqr_output.txt
```

The two `*_eval_output.txt` logs stay local. The required video is one uncut recording containing the final PID evaluation followed by the final LQR evaluation, with each result panel visible. The zip and an unlisted video link are submitted through the e-Yantra portal.

## Current status

The symbolic torque-input model and submission requirements are documented. The numeric parameter mapping and motor-velocity input mapping still need verification. No Task 2A evaluator score or output file has been produced, so this is a working plan rather than a completed solution.
