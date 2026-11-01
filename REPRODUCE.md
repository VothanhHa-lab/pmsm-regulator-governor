# Reproducing the results

Tested with Python 3.13 and the package versions in `requirements.txt`, on Linux. Run times are for two cores.
Random seeds are fixed in the scripts: 1–3 for development, 231–240 for the step tests, 231–233 for the vehicle scenarios (V1: 231–232).

## Level 1 — regenerate the paper from stored results (about 1 min)

```bash
cd paper
FCS_CODE=../code/ python3 make_assets_v7.py   # writes fig/*.pdf, table_*.tex, auto_macros.tex
python3 build_pdflatex.py                      # IEEEtran version (needs TeX Live)
```

The simulation results quoted in the text and tables are macros in `auto_macros.tex`, generated from the stored data; the few rounded values in the prose (for example "about 1.6 ms") are derived from these macros.

## Level 2 — re-run the simulations

```bash
cd code
python3 theory/check_reduction.py      # Lemma 1, Theorem 1, N*        -> results/nstar.json, theory_check.json
python3 theory/robust_margin.py        # Theorem 2 margins             -> results/robust_margin.json
python3 dev_design.py                  # Fig. design sweep (dev seeds) -> results/design_sweep.csv
python3 dev_mpc.py && python3 dev_tight.py   # MPC and tightening choices -> results/mpc_choice.json, tight_choice*.json
python3 precompute_oinf.py             # O∞ sets, cached in results/oinf_cache.pkl                              (~1 h)
python3 eval_v7.py                     # step tests                    -> results/final7/step.csv, paired.csv   (~1 h)
python3 trace_v7.py                    # traces for figures            -> results/final7/trace.csv
python3 eval_vehicle.py                # vehicle scenarios             -> results/vehicle7/                     (~1 h)
```

## Level 3 — embedded C

```bash
cd code/embedded
python3 gen_vectors.py && WITH_OINF=1 python3 gen_vectors.py
gcc -O2 -std=c11 -o test_host test_host.c rg.c -lm && ./test_host   # checks C against Python, writes host_timing.json
```

`code/embedded/README.md` describes the processor-in-the-loop harness for STM32 and C2000. It has not yet been run on hardware.

## Map from paper to sources

| Paper item | Data | Script |
|---|---|---|
| Table of parameters | `code/params.py`, `code/SOURCES.md` | – |
| Theorem 1 (N*), Theorem 2 margins | `nstar.json`, `theory_check.json`, `robust_margin.json` | `theory/*.py` |
| Design-parameter figure | `design_sweep.csv` | `dev_design.py` |
| Transient-trajectory figure | `final7/trace.csv` | `trace_v7.py` |
| Speed × frequency grid figure, step-test tables, trade-off table | `final7/step.csv`, `final7/paired.csv` | `eval_v7.py` |
| Robustness table (parameter errors, saturation, Vdc −15 %) | `final7/step.csv` (group `Rob`) | `eval_v7.py` |
| Vehicle table and figure | `vehicle7/vehicle.csv`, `vehicle7/trace_*.csv.gz` | `eval_vehicle.py` |
| Computational-cost table | `embedded/host_timing.json`, `code_size_m4.json`, `results/oinf_rows.json` | `embedded/test_host.c` |

## Notes

- `results/final7/eval_v7_as_run.py` is the exact script version that produced `step.csv`.
- After the reference generator was changed to the measured DC voltage, the affected cases were re-run with `rerun_vdc.py`; earlier values are kept in `*_before_vdcfix.csv`.
- `results/oinf_cache.pkl` is not stored (about 5 MB); `precompute_oinf.py` rebuilds it.
