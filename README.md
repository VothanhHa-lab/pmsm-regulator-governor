# Closed-form reference governor for discrete-domain PMSM current regulators — code and data

This repository accompanies the manuscript

> T. H. Vo, "Closed-Form Reference Governor for Discrete-Domain PMSM Current Regulators: Exact Loop Reduction and Recursive Feasibility in Field Weakening," submitted to *IEEE Access*.

All results are from simulation. No hardware-in-the-loop, processor-in-the-loop or bench test has been carried out.

## Contents

| Path | Content |
|---|---|
| `code/plant.py`, `simulate.py`, `params.py` | Switching-inverter PMSM plant (two-level inverter, dead time, sampling delay, optional saturation) and simulation loop |
| `code/controllers.py` | Discrete-domain regulator (DCVR), proposed governor (one-step and N-step), tightened governor, anti-windup, constrained MPC, O∞ governor, ramps, complex-vector PI |
| `code/vehicle_sim.py` | Road-load model, driver and traction control for the vehicle scenarios V1–V3 |
| `code/theory/` | Numerical checks of Lemma 1 and Theorems 1–2 |
| `code/eval_v7.py`, `trace_v7.py`, `eval_vehicle.py` | Final evaluation on the unseen seeds 231–240 and vehicle scenarios |
| `code/dev_*.py` | Development and tuning scripts (seeds 1–3; not used for reported numbers) |
| `code/precompute_oinf.py` | Offline maximal output admissible sets for the O∞ baseline |
| `code/embedded/` | C implementations (float32), host tests against Python, processor-in-the-loop harness for STM32/C2000 (not yet run on hardware) |
| `code/results/` | Raw results of every run, including failed and divergent runs |
| `paper/` | LaTeX sources; `make_assets_v7.py` regenerates every figure, table and number in the text from `code/results/` |
| `DATA_DICTIONARY.md` | Column definitions and units of all result files |
| `REPRODUCE.md` | Which script produces which figure, table and number |

## Quick start

```bash
python3 -m venv .venv && . .venv/bin/activate
pip install -r requirements.txt
# regenerate all figures, tables and numbers of the paper from the stored results (about 1 min)
cd paper && FCS_CODE=../code/ python3 make_assets_v7.py
```

The full re-simulation is described in `REPRODUCE.md` (about 3 h on two cores).

## Parameters

The motor parameters are equivalent values derived from published ratings of a rear-wheel-drive electric car (sources in `code/SOURCES.md`). They are not measurements of a production motor, and the manufacturer has not been involved in this work.

## Licenses

- Code: MIT License (`LICENSE`).
- Data in `code/results/`: Creative Commons Attribution 4.0 International (`LICENSE-DATA`).

## Citation

Please cite the article and this repository; see `CITATION.cff`.

## Contact

Thanh Ha Vo, University of Transport and Communications, Hanoi, Vietnam — vothanhha.ktd@utc.edu.vn
