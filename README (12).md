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

| File | What it is |
|---|---|
| `tpw.py` | All the code: the Colab notebook of the reported run, exported as a Python file. It defines every function, then runs Steps 1–14. |
| `requirements.txt` | Python packages, pinned to the versions of the reported run. |
| `r_results_and_versions.zip` | The R part of the reported run (contents below). |
| `tables_full.zip` | Every result table of the reported full run: 85 CSV files. |
| `CITATION.cff` | How to cite the paper and this package. |

`r_results_and_versions.zip` contains:

| Path in the zip | What it is |
|---|---|
| `data/published_rules/` | Stored results of every R computation (seven CSV files; see *R benchmarks*). With them the code reproduces the reported R-based numbers without running R. |
| `R/r_benchmarks.R`, `R/eqd.R` | The R scripts: Bayesian threshold averaging (threshr), ForwardStop (eva) and EQD (mev). |
| `outputs/cache/full/.../part0.csv`, `R_log_part0.txt` | The raw output and log of each R job. |
| `software_versions.txt` | Python, R and package versions of the reported run, with R `sessionInfo()`. |
| `outputs/manifest_full.json` | Settings, seed, software versions, warnings and SHA-256 checksums of every data file of the reported run. |

The figures are not stored: the code draws them (Steps 2, 11 and 13) and saves them in
`outputs/figures/full/` as PDF, SVG, EPS and 600-dpi PNG. Market price snapshots are not included
(see *Data*).

## Requirements

- **Python 3** (the reported run used Python 3.13.16) and the packages in `requirements.txt`.
- **R (optional)** with threshr, eva and mev, for rerunning the external benchmarks. The reported run used
  R 4.6.1, threshr 1.0.8, eva 0.2.7, mev 2.2, revdbayes 1.5.7 and rust 1.4.4.
- An internet connection on the first run, to download the data.

```bash
pip install -r requirements.txt
Rscript -e 'install.packages(c("threshr", "eva", "mev"))'     # optional
```

## How to run

`tpw.py` is a notebook exported from Google Colab. It contains notebook commands (lines starting with
`!`, and `display`), so run it as a notebook, not with `python tpw.py`.

1. **Set up the project folder.** Clone or download the repository, then unpack the stored R results into
   the same folder:

   ```bash
   git clone https://github.com/gwananii/tpw-expected-shortfall
   cd tpw-expected-shortfall
   unzip r_results_and_versions.zip      # creates data/, R/, outputs/ and software_versions.txt
   ```

2. **Make the notebook.**

   ```bash
   jupytext --to ipynb tpw.py            # writes tpw.ipynb
   ```

   Open `tpw.ipynb` in Jupyter (`jupyter notebook tpw.ipynb`), or upload it to Google Colab. In Colab,
   upload the repository folder to Google Drive and run from it, so that the stored results are found
   and the cache survives a disconnect.

3. **Choose the settings.** They are at the top of the notebook, and this is the only cell you normally
   change:

   | Setting | Meaning |
   |---|---|
   | `USER_RUN_MODE` | `"quick"`: smoke test with small samples, which runs in minutes; its numbers are not meaningful. `"full"`: the full study, which takes hours. |
   | `USER_N_JOBS` | Number of parallel processes (`-1` = all cores). |
   | `USER_RUN_R` | `True`: rerun the R benchmarks if `Rscript` is found. `False`: read the stored R results. To reproduce the paper's numbers exactly, set it to `False`. |
   | `USER_SAVE_SAMPLES` | Save every simulated sample to `data/simulated/`, about 50 MB in full mode. |

4. **Run all cells in order.** The library cells only define functions. A self-check cell verifies the
   GPD fit and the POT formulas against SciPy and against stored reference values. The Step cells at the
   end do the work.

Some notes on running:

- **Caching:** every block of work is cached in `outputs/cache/<mode>/`, so an interrupted run resumes
  where it stopped.
- **Install cells:** a few cells begin with `!pip` or `!Rscript`. They install packages in Colab and can
  be skipped when the packages are already installed.
- **Colab-only cells:** the last two cells, after Step 14, zip the tables and save the R results. They
  use Colab paths (`/content`) and `google.colab`, so they work only in Colab. Skip them elsewhere; the
  results are already in `outputs/`.
- **Checking your tables:** to compare your tables with the paper's, unpack `tables_full.zip` into
  another folder. Your run writes its own tables to `outputs/tables/full/`.

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

The tables are in `tables_full.zip` (and, after a run, in `outputs/tables/full/`). The figures are
written to `outputs/figures/full/` when the code is run.

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
written to the manifest) with those in `outputs/manifest_full.json` (in `r_results_and_versions.zip`). If they differ, the market results
can differ slightly from the paper. The insurance results are unaffected.

## R benchmarks

The Bayesian threshold averaging (threshr) and EQD (mev) results are Monte Carlo based. They vary
between R runs, and in weakly identified years they are not reproducible to more than a few digits;
Section 4.5 of the paper reports this. The paper reports one complete run, whose R results are stored
in `data/published_rules/` inside `r_results_and_versions.zip`:

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
- **Run record:** the manifest (`outputs/manifest_full.json`) records the settings, software versions, data checksums and every warning
  of the run.

## Citation

Gwanani, I.; Dhliwayo, L.; Nadarajah, S.; Makurumidze, S. Threshold-Averaged Expected Shortfall Under
Weak Identification. *Risks* (submitted). Replication package: [DOI].

## License

[license]
