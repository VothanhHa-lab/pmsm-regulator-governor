# Data dictionary

All quantities are from simulation. Units are SI unless stated. Each row is one run; failed and divergent runs are kept.

## `code/results/final7/step.csv` — torque-step tests (seeds 231–240)

| Column | Meaning | Unit |
|---|---|---|
| `group` | Test group: `Main` (speed × switching-frequency grid), `Between` (speeds between grid points), `Size` (other step sizes), `Rob` (plant parameter changed) | – |
| `name` | Controller and switching frequency, e.g. `DCVR 3` = unmodified regulator at 3 kHz, `NS 3` = proposed N-step governor, `RNS` = tightened governor, `Hanus` = conditioning anti-windup, `Dprio` = d-axis-priority anti-windup, `Reg` = one-step governor (Proposition 1, voltage limit only), `MPC` = constrained MPC, `OInf` = O∞ governor, `Sched` = speed-scheduled ramp, `Slew120`/`Slew400` = fixed ramps (A/ms), `CVPI` = complex-vector PI | – |
| `n` | Motor speed, held constant | r/min |
| `up` | `True` for a step up, `False` for a step down | – |
| `Ta`, `Tb` | Torque demand before and after the step at t = 0.1 s | N·m |
| `cs` | Plant case: `Nominal`, `Rs+50%`, `Ld,Lq±20%`, `Ld,Lq-30%`, `Lq-20%`, `psi-10%`, `psi-15%`, `psi+5%`, `Hot` (ψ_f −10 %, R_s +40 %), `Sat3000`/`Sat1500`/`Sat800` (saturation with I_s in A), `Vdc-15%` | – |
| `seed` | Random seed (initial rotor angle, measurement noise) | – |
| `div` | 1 if the run diverged or did not settle (Tss is NaN) | – |
| `t90` | Torque rise time from the step to 90 % of the change | ms |
| `over` | Torque overshoot beyond the final value | N·m |
| `ipk` | Peak sampled current magnitude after the step | A |
| `ipk_avg` | Peak of the current magnitude averaged over one PWM period | A |
| `Tss` | Final torque (mean over the last part of the run) | N·m |
| `Tstd` | Standard deviation of torque over the last part of the run (ripple) | N·m |
| `Terr` | Steady torque error relative to the model torque of the final reference | % |
| `fsw` | Mean device switching frequency | kHz |
| `hold` | Updates at which the constraint was already violated at α = 0 (governor holds the reference) | count |
| `stall` | Updates with α = 0 | count |
| `infeas` | Updates at which the MPC found no feasible voltage vector | count |
| `rows` | Rows of the O∞ set used at the last update | count |

`step_before_vdcfix.csv` and `paired_before_vdcfix.csv` keep the results obtained before the reference generator was switched to the measured DC voltage (see `rerun_vdc_log.txt`); they are not used in the paper.

## `code/results/final7/paired.csv` — paired differences over seeds

`group, n, Ta, cs, f` identify the test (f = switching frequency, kHz); `base` is the comparison controller; `metric` is one of `over, t90, ipk, ipk_avg`; `diff` is the mean of (proposed − base) over seeds and `lo`, `hi` its 95 % confidence interval.

## `code/results/final7/trace.csv` — time traces for figures

`name, n` as above; `t` time (s); `Te` torque (N·m); `id, iq` currents (A); `idr, iqr` references after the governor (A); `alpha` governor step fraction (–); `vmag` regulator voltage magnitude (V).

## `code/results/vehicle7/vehicle.csv` — vehicle scenarios (seeds 231–233; V1: 231–232)

| Column | Meaning | Unit |
|---|---|---|
| `sc` | Scenario: `V1` urban (ECE-15 part 3), `V2` uphill overtaking (5 % grade, 100→130 km/h), `V3` wet road (μ 0.6, patch 0.25 at 1.0–1.3 s) | – |
| `name`, `seed`, `div`, `fsw`, `ipk_avg` | As in `step.csv` | – |
| `Terr_rms`, `Terr_max` | RMS and maximum torque error against the torque reference | N·m |
| `Tover` | Largest excess of torque over the reference | N·m |
| `alpha_busy` | Fraction of updates with α < 0.999 | – |
| `v_end` | Vehicle speed at the end of the run | km/h |
| `t_130` | Time to reach 130 km/h (V2 only) | s |
| `lam_max` | Peak wheel slip (V3 only) | – |
| `v_rmse`, `v_max_err` | RMS and maximum speed tracking error (V1 only) | km/h |

`trace_<scenario>_<controller>.csv.gz`: `t` (s), `Te` (N·m), `id, iq` (A), `w` electrical speed (rad/s), `idr, iqr` references (A). V1 traces are sampled at 1 ms.

## Other files in `code/results/`

| File | Content | Produced by |
|---|---|---|
| `nstar.json`, `theory_check.json` | Tail index N* and numerical check of Lemma 1 and Theorem 1 | `theory/check_reduction.py` |
| `robust_margin.json` | Measured prediction-update bounds against the margin of Theorem 2 | `theory/robust_margin.py` |
| `design_sweep.csv` | Sweep of V_g and k_z on development seeds | `dev_design.py` |
| `mpc_choice.json`, `dev_mpc.csv` | MPC horizon and weight chosen on development seeds | `dev_mpc.py` |
| `tight_choice*.json` | Tightening margins of the robust governor | `dev_tight.py` |
| `oinf_rows.json` | Number of O∞ rows per speed | `eval_v7.py` |
| `sched_table.json` | Speed-scheduled ramp table | `dev_sched.py` |
| `*_log.txt` | Console logs of the runs | – |

Files named `event_*`, `lambda_sweep*`, `train_history.csv`, `weight_net.npz`, `paired_ai_vs_tuned.csv` and `fig_event_*` belong to earlier versions of the study (event-pulse method and the withdrawn neural-network weighting preprint) and are not used in the paper.

## `code/embedded/`

`host_timing.json`: mean execution time on the host computer (ns). `code_size_m4.json`: code size for Cortex-M4F, clang 18, `-O2` (bytes). `vec_*.txt`: recorded test vectors. `oinf_<speed>.txt`: O∞ constraint rows exported for the C implementation.
