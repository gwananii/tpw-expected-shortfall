# Threshold-Averaged Expected Shortfall Under Weak Identification — replication package

Isaac Gwanani, Lawrence Dhliwayo, Saralees Nadarajah and Shepard Makurumidze

Code and stored results that reproduce every table and figure of the paper
*Threshold-Averaged Expected Shortfall Under Weak Identification*, submitted to *Risks* (MDPI),
manuscript risks-4730816.

Repository DOI: [DOI]

Contact: Isaac Gwanani (corresponding author), Faculty of Mathematics and Computational Science,
University of Zimbabwe, Harare, Zimbabwe

---

## What the paper does

Peaks-over-threshold (POT) estimates of value-at-risk (VaR) and Expected Shortfall (ES) depend on the
threshold. The paper:

- averages the estimates over a grid of thresholds with several weighting schemes: equal weights (EW),
  CV-BMA, VPW, target-specific precision weights (TPW) and Bayesian averaging (Northrop et al.);
- compares protections against near-singular shape estimates, where a fitted shape close to one makes
  the ES explode: the median across thresholds, screened EW, a capped shape, penalized likelihood and a
  bias-reduced Hill estimator;
- evaluates checks that recognize samples in which the ES is weakly identified. The main one is the
  shape bound: the 90th percentile of the bootstrap shape at q = 0.91 is one or more. A likelihood-ratio
  test of a shape of one is also evaluated;
- applies everything to simulated samples, to five loss data sets (Danish, Norwegian and French fire
  losses; BMW and Siemens returns) and to six daily market series, in full samples and in rolling
  one-year windows.

## Contents

| Path | What it is |
|---|---|
| `TPW.ipynb` | The notebook. It defines all functions, then runs Steps 1–14. |
| `requirements.txt` | Python packages, with the versions of the reported run where pinned. |
| `R/r_benchmarks.R`, `R/eqd.R` | R scripts for the external benchmarks: Bayesian threshold averaging (threshr), ForwardStop (eva) and EQD (mev). The notebook writes them. |
| `data/published_rules/` | Stored results of every R computation from the reported run. With these files the notebook reproduces the reported R-based numbers without running R (see *R benchmarks*). |
| `software_versions.txt` | Python, R and package versions of the reported run, including R `sessionInfo()`. |
| `outputs/manifest_full.json` | Settings, seed, software versions, warnings and SHA-256 checksums of every data file of the reported run. |
| `outputs/tables/full/` | Every result table of the reported run, as CSV. |
| `outputs/figures/full/` | Every figure of the reported run, as PDF, SVG, EPS and 600-dpi PNG. |

The notebook creates `data/` and `outputs/` in the folder it is run from. Market price snapshots
(`data/clean/`) are not included (see *Data*).

## Requirements

- **Python 3** with numpy, scipy, pandas, matplotlib, joblib, yfinance, rdata and IPython. The reported
  run used Python 3.13, NumPy 2.4.6, SciPy 1.16.3 and pandas 2.2.3; `software_versions.txt` lists the
  rest.
- **R (optional)** with threshr, eva and mev, for the external benchmarks. The reported run used R 4.6.1,
  threshr 1.0.8, eva 0.2.7, mev 2.2, revdbayes 1.5.7 and rust 1.4.4.
- An internet connection on the first run, to download the data.

```bash
pip install -r requirements.txt
Rscript -e 'install.packages(c("threshr", "eva", "mev"))'     # optional
```

## How to run

1. Put `TPW.ipynb` in an empty project folder, or in the unpacked repository so that the stored R
   results in `data/published_rules/` are found. Open it with Jupyter or Google Colab.
2. Edit the settings cell, the only cell you normally change:

   | Setting | Meaning |
   |---|---|
   | `USER_RUN_MODE` | `"quick"`: smoke test with small samples, which runs in minutes; its numbers are not meaningful. `"full"`: the full study, which takes hours. |
   | `USER_N_JOBS` | Number of parallel processes (`-1` = all cores). |
   | `USER_RUN_R` | `True`: run the R benchmarks if `Rscript` is found. `False`: read the stored R results. |
   | `USER_SAVE_SAMPLES` | Save every simulated sample to `data/simulated/`, about 50 MB in full mode. |

3. Run all cells in order. The library cells only define functions, and a self-check cell verifies
   the GPD fit and POT formulas against SciPy and against stored reference values. The Step cells at the
   end do the work.

Every block of work is cached in `outputs/cache/<mode>/`, so an interrupted run resumes where it
stopped. In Colab, run from a folder on Google Drive so that the cache survives a disconnect.

Some cells begin with `!pip` or `!Rscript`. They install packages in Colab and can be skipped when the
packages are already installed.

## Steps

| Step | Content | Main outputs |
|---|---|---|
| 1 | Data: download or load, archive and summarize every data set | `data_summary` |
| 2 | Precision of the per-threshold estimates (theory and simulation) | Tables 1–3, Figure 1 |
| 3 | Threshold-weighting schemes in simulation | weighting accuracy tables |
| 4 | Robustness of the weighting results | robustness tables |
| 5 | Protections against near-singular estimates | protection accuracy and capital-cost tables |
| 6 | External benchmarks in R (Bayesian averaging, ForwardStop) | `r_benchmarks_stress` |
| 7 | Market data, full samples | `market_levels` (Table 9) |
| 8 | Rolling GARCH-EVT backtests | `backtest_summary` |
| 9 | Danish fire losses, weighting schemes | Danish weighting tables |
| 10 | Norwegian fire claims (nominal), weighting schemes | Norwegian nominal tables |
| 11 | Insurance losses with every protection | Tables 6–7, Figures 3–5 |
| 12 | Near-singular fits by number of exceedances; run times | decay slopes, `run_times` |
| 13 | Reliability checks, additional designs and data, identification | Tables 4, 5 and 8, Figures 2 and 6 |
| 14 | All assets by sample regime: one-year market windows, raw and GARCH-filtered | Tables 9–11 |
| Summary | Integrity checks and the manifest | `outputs/manifest_<mode>.json` |

## Where each table and figure comes from

All files are in `outputs/tables/full/` and `outputs/figures/full/`.

| Paper | File(s) |
|---|---|
| Table 1 | `asymptotic_dispersion.csv` |
| Table 2 | `near_singular_probability.csv` |
| Table 3 | `finite_sample_dispersion.csv` |
| Table 4 | `by_design_es99_all.csv`, `by_design_es99_boundary.csv` |
| Table 5 | `checks_es99.csv`, `checks_by_n.csv` |
| Table 6 | `danish_protections.csv` |
| Table 7 | `norwegian_real_protections.csv` |
| Table 8 | `data_stability.csv`, `out_of_sample_inference.csv` |
| Table 9 | `market_levels.csv`, `market_windows_stability_es99.csv` |
| Table 10 | `market_windows_by_flag_es99.csv`, `market_filtered_by_flag_es99.csv` |
| Table 11 | `regime_summary.csv` |
| Figure 1 | `precision_profile` |
| Figure 2 | `es_profile_likelihood` |
| Figure 3 | `danish_protections_es99` |
| Figure 4 | `norwegian_protections_es99` |
| Figure 5 | `fitted_shapes` |
| Figure 6 | `decision_flowchart` |

The remaining CSV files are supplementary results.

## Data

The notebook downloads every data set on first use and archives it, with SHA-256 checksums, under
`data/`.

| Data | Source | Used for |
|---|---|---|
| Danish fire losses, 1980–1990 | dataset `danish`, R package evir (CRAN) | Steps 9, 11, 13 |
| Norwegian fire claims, nominal | dataset `norwegianfire`, R package ReIns (CRAN) | Step 10 |
| Norwegian fire claims at 2012 prices | dataset `norfire`, R package CASdatasets 1.2-1 (GitHub `dutangc/CASdatasets`) | Steps 11, 13 |
| French commercial fire losses, 1982–1996 | dataset `frecomfire`, CASdatasets 1.2-1 | Step 13 |
| BMW and Siemens daily returns, 1973–1996 | datasets `bmw` and `siemens`, evir | Step 13 |
| Six daily market series, 2005–2023 | Yahoo Finance via yfinance: S&P 500 (`^GSPC`), EUR/USD (`EURUSD=X`), gold (`GC=F`), WTI crude oil (`CL=F`), 10-year Treasury note futures (`ZN=F`), Bitcoin (`BTC-USD`) | Steps 7, 8, 14 |

**Market data.** The Yahoo Finance prices are not redistributed. On the first run the notebook downloads
them and saves snapshots in `data/clean/`; later runs reuse the snapshots (`DOWNLOAD_FRESH = False`).
Yahoo Finance can revise past prices, so compare the checksums of your snapshots (printed by Step 1 and
written to the manifest) with those in `outputs/manifest_full.json`. If they differ, the market results
can differ slightly from the paper. The insurance results are unaffected.

## R benchmarks

The Bayesian threshold averaging (threshr) and EQD (mev) results are Monte Carlo based. They vary
between R runs, and in weakly identified years they are not reproducible to more than a few digits;
Section 4.5 of the paper reports this. The paper reports one complete run, whose R results are stored
in `data/published_rules/`:

| File | Job |
|---|---|
| `stress_results.csv` | Bayesian averaging and ForwardStop, simulation designs |
| `danish_results.csv` | Bayesian averaging, Danish fire losses |
| `norwegian_results.csv` | Bayesian averaging, Norwegian fire claims (nominal) |
| `norfire_real_results.csv` | Bayesian averaging, Norwegian fire claims at 2012 prices |
| `eqd_data_results.csv` | EQD, loss data sets |
| `eqd_stress_results.csv` | EQD, simulation designs |
| `bayes_new_data_results.csv` | Bayesian averaging, French fire, BMW and Siemens |

With `USER_RUN_R = False`, or when `Rscript` is not found, the notebook reads these files and
reproduces the reported numbers exactly. With `USER_RUN_R = True` and R installed, it reruns the R jobs,
and the Bayesian and EQD numbers will differ somewhat from the paper. Everything computed in Python is
unaffected.

## Reproducibility

- **Random numbers:** all randomness comes from a master seed (`SEED = 20240601`). Every sample and
  every bootstrap draws from its own stream keyed by fixed labels, so results do not depend on run order
  or on the number of processes.
- **Python numbers:** these are deterministic. Across platforms they agree to floating-point precision;
  the self-check allows a relative difference of 1e-6.
- **Quick mode:** quick and full results are cached separately, and quick-mode numbers are not the
  paper's.
- **Run record:** the manifest records the settings, software versions, data checksums and every warning
  of the run.

## Citation

Gwanani, I.; Dhliwayo, L.; Nadarajah, S.; Makurumidze, S. Threshold-Averaged Expected Shortfall Under
Weak Identification. *Risks* (submitted). Replication package: [DOI].

## License

[license]
