Expected Shortfall under threshold uncertainty: target-specific precision weighting (TPW)

Replication code for the paper

Gwanani, Isaac., Dhliwayo, Lawrence., Nadarajah, Saralees. and Makurumidze, Shepard. Expected Shortfall under threshold uncertainty: target-specific weighting of peaks-over-threshold estimates. Submitted to the Journal of Risk and Financial Management.

Peaks-over-threshold (POT) estimates of Expected Shortfall (ES) and deep quantiles depend on the choice of threshold. Averaging over thresholds helps only if the weights follow the risk measure that is reported. TPW weights each threshold of a grid by

w_j ∝ 1 / (s_j² + τ̂_j²)

where s_j is the bootstrap standard error of the reported measure itself (for example the 97.5% ES) at threshold j, and τ̂_j² is a debiased estimate of that threshold's systematic deviation from the precision-weighted consensus. One bootstrap provides everything.


In 2,400 stress samples, TPW cuts the relative RMSE of ES at 97.5% from 103% (equal weights) and 191% (cross-validated BMA) to 25%. It also makes stressed ES more stable under monthly recalibration. This repository reproduces every table and figure of the paper.

Contents
Replication.ipynb          the paper, part by part (start here)
run_all.py                 the same from the command line
TPW_replication_single_file.py   all code in one file, as notebook cells to paste
tailrisk/                  the Python package
  config.py                all settings: seed, threshold grid, run modes, folders
  gpd.py                   GPD maximum likelihood, POT VaR and ES
  weights.py               EW, naive BMA, CV-BMA, VPW, TPW-core, TPW, TMV
  estimators.py            per-threshold estimates, bootstrap, all schemes for one sample
  simulate.py              the eight stress designs and their true values
  theory.py                Tables 1-3
  garch.py, backtest.py    GARCH(1,1)-t filter, rolling forecasts, ES backtests
  experiments.py           cached computations behind every table
  tables.py, figures.py    paper tables and figures
  data.py                  price snapshots, losses, Danish fire losses
R/published_rules.R        Northrop et al. (2017, threshr) and ForwardStop (eva) benchmarks
data/                      Danish fire losses, stored R results, price snapshots (data/clean/)
tests/test_core.py         unit tests and a regression test against the original results
Quick start
bash
git clone https://github.com/<user>/<repository>.git
cd <repository>
pip install -r requirements.txt
python tests/test_core.py          # about 1 minute
python run_all.py --mode quick     # every step with small sizes, about 10 minutes
python run_all.py --mode full      # the paper's settings

You can also open Replication.ipynb, set RUN_MODE, and run all cells.

Mode	What it does	Time
quick	Every part runs with a few samples and small bootstraps. Use it to check the installation. Its numbers are not the paper's.	about 10 min
full	The settings of the paper.	about 2.5 CPU-hours, plus 3–4 h if R is used

Results are cached block by block in outputs/cache/<mode>/, so an interrupted run resumes where it stopped. Tables are written to outputs/tables/<mode>/ and figures to outputs/figures/<mode>/ (PDF and 600-dpi PNG). outputs/manifest_<mode>.json records package versions, settings, SHA-256 checksums of all data files and any warnings.

Paper map
Paper	Notebook part / function	Output
Section 4, Tables 1–3, Figure 1	Part 1 theory()	asymptotic dispersion, explosion probabilities, precision profile
Section 5, Tables 4–6, C1–C3, Figure 2	Part 2 simulation()	2,400 stress samples and exact GPD samples
Section 5.5, Table 7	Part 3 published_rules()	Bayesian and ForwardStop benchmarks (R)
Section 6.1, Tables 8–9	Part 4 market()	market levels and stressed 250-day windows
Section 6.2, Table C4	Part 5 backtests()	rolling GARCH-EVT ES backtests
Section 6.3, Tables 10, C5, Figure 3	Part 6 danish()	Danish fire losses
Data
Market prices. Daily closes from Yahoo Finance, 2005–2023: S&P 500 (^GSPC), EUR/USD (EURUSD=X), gold (GC=F), WTI (CL=F), 10-year Treasury note futures (ZN=F) and Bitcoin (BTC-USD). The analysis reads the snapshots data/clean/<ASSET>_clean.csv. If a snapshot is missing, it is downloaded with yfinance and archived. Yahoo revises its history, so a fresh download can differ slightly from the snapshots used in the paper. Their checksums are listed in the manifest. [[State here whether the snapshots are included in this repository; Yahoo's terms restrict redistribution.]]
Danish fire losses. The 2,167 losses of McNeil (1997), 1980–1990, from the dataset danish of the CRAN package evir. If the file is missing, the code downloads it and checks that it is the paper's sample (needs pip install rdata, or R).
Simulated samples. Every simulated sample is saved to data/simulated/ when it is generated.
Published-rule results. data/published_rules/ holds the results of the paper's R run, so Table 7 and Table 10 can be reproduced without R.
Reproducibility

Every simulated sample and every bootstrap draws from its own random stream, keyed by the master seed (20240601) and a fixed label. Results therefore do not depend on execution order or on the number of parallel processes. The regression test recomputes stored results of the study; on Linux and Windows they agree to a relative difference of about 1e-8. The TMV benchmark uses a numerical optimiser (SLSQP), whose path depends slightly on the SciPy version, so TMV entries can differ by a few tenths of a percentage point between installations. All other entries are reproduced to the precision printed in the paper.

Requirements

Python ≥ 3.10 with numpy, scipy, pandas, joblib and matplotlib. yfinance is needed only if price snapshots are missing, and rdata only to read the Danish data. R is optional: R ≥ 4.3 with threshr 1.0.8 and eva 0.2.7. Tested on Linux (Python 3.13) and Windows (Anaconda).

Using TPW on your own data
python
import numpy as np
from tailrisk.estimators import estimate_all

losses = np.loadtxt("my_losses.csv")              # losses as positive numbers
res = estimate_all(losses, np.random.default_rng(1), B=200)
print(res["tpw_es0.975"], res["ew_es0.975"], res["range_es0.975"])

res contains every scheme (single, ew, cv, cv_loo, vpw, tpw_core, tpw, tmv) for VaR at 97.5/99/99.5/99.9% and ES at 97.5/99%. It also gives diagnostics such as the effective number of thresholds and the range of single-threshold estimates (in % of their mean).

Citation
bibtex
@article{gwanani_tpw,
  author  = {Gwanani, Isaac and Dhliwayo, L. and Nadarajah, S. and Makurumidze, S.},
  title   = {Expected Shortfall under threshold uncertainty: target-specific weighting of peaks-over-threshold estimates},
  journal = {Journal of Risk and Financial Management},
  year    = {[[year]]},
  note    = {Submitted}
}

Archived release: [[Zenodo DOI]].

License

[[MIT for the code (add a LICENSE file); the data keep the terms of their sources.]]

Contact

Isaac Gwanani, University of Zimbabwe, gwananii@gmail.com
