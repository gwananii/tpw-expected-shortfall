Expected Shortfall under threshold uncertainty: target-specific precision weighting (TPW)
Replication code for the paper

> Gwanani, Isaac., Dhliwayo, Lawrence., Nadarajah, Saralees. and Makurumidze, Shepard. *Expected Shortfall under threshold uncertainty: target-specific weighting of peaks-over-threshold estimates.* Submitted to the *Journal of Risk and Financial Management*.
> 
What the paper does

Peaks-over-threshold (POT) estimates of Expected Shortfall (ES) and deep quantiles depend on the choice of threshold. Averaging over thresholds helps only if the weights follow the risk measure that is reported. TPW weights each threshold of a grid by
```
w_j ∝ 1 / (s_j² + τ̂_j²)
```
`s_j` is the bootstrap standard error of the reported measure itself (for example the 97.5% ES) at threshold `j`.
`τ̂_j²` is a debiased estimate of that threshold's systematic deviation from the precision-weighted consensus of all thresholds.

One bootstrap provides everything.

In 2,400 stress samples, TPW cuts the relative RMSE of ES at 97.5% from 103% (equal weights) and 191% (cross-validated BMA) to 25%. It leaves the 99% VaR unchanged. In rolling 250-day windows it lowers the average monthly change of the 99% ES from 10.1% to 6.2%.

Repository contents

File	What it is

`TPWPhd.ipynb`	The complete code and the output of the full run (`USER_RUN_MODE = "full"`). Run the cells in order; each cell is labelled `CELL n` and `PART n`.

`README.md`	This file

The notebook is self-contained: all functions are defined in its cells (CELL 0–14), followed by checks (CELL 16), the data download, and one cell per part of the paper.

Notebook part	Paper	Output

CELL 16	–	Checks: GPD fit, POT formulas, and reproduction of the study's stored results
Part 0	Section 3.6	Data check: sample sizes and SHA-256 checksums of the price files
Part 1	Section 4, Tables 1–3, Figure 1	Asymptotic dispersion, explosion probabilities, precision profile
Part 2	Section 5, Tables 4–6, C1–C3, Figure 2	2,400 stress samples and 1,600 exact GPD samples
Part 3	Section 5.5, Table 7	Bayesian (threshr) and ForwardStop (eva) benchmarks; needs R
Part 4	Section 6.1, Tables 8–9	Market levels and stressed 250-day windows
Part 5	Section 6.2, Table C4	Rolling GARCH-EVT ES backtests (the slowest part)
Part 6	Section 6.3, Tables 10, C5, Figure 3	Danish fire losses
The saved notebook shows the full-mode output of Parts 0–4. These reproduce the paper's tables, except for the TMV benchmark (see Reproducibility). Part 3 was skipped because R was not installed, and Parts 5–6 are not yet included.

How to run

Install Python ≥ 3.10 with `numpy scipy pandas joblib matplotlib`, plus `yfinance` and `rdata`.
Put the price files in `data/clean/` (see Data). If a file is missing, the notebook downloads it from Yahoo Finance and saves it.
Open `TPWPhd.ipynb` and choose the run mode in CELL 0:
`USER_RUN_MODE = "quick"`: every part with small sizes, about 10 minutes, for checking the installation. Its numbers are not the paper's.
`USER_RUN_MODE = "full"`: the paper's settings.
Run all cells. Results are saved block by block in `outputs/cache/full/`, so an interrupted run continues where it stopped. Tables go to `outputs/tables/full/`, figures to `outputs/figures/full/`, and `outputs/manifest_full.json` records versions, settings and data checksums.
R is optional. With R ≥ 4.3 and the packages `threshr` (1.0.8) and `eva` (0.2.7), Part 3 also runs the published threshold rules.
Data
Market prices. Daily closes from Yahoo Finance, January 2005 to December 2023, archived as fixed snapshots `data/clean/<ASSET>_clean.csv`. Yahoo revises its history, so a fresh download can differ slightly; the checksums below identify the files used in the paper.
File	Instrument (ticker)	Losses analysed	SHA-256
`SP500_clean.csv`	S&P 500 (`^GSPC`)	4,780	`ec2f485bc8e2ae785a4246ba88e8d5524b3d38c1da3b21ad9ddde7181d113ff2`
`EURUSD_clean.csv`	EUR/USD (`EURUSD=X`)	4,926	`d05da6bf8ec016c9df87f350d2d81509548bbed26362926cefadeaa18653c5e1`
`GOLD_clean.csv`	Gold futures (`GC=F`)	4,774	`f4746cb467b46865ca5bd9b8d3b090aa6735bd9550252118985435178e48809b`

`WTI_clean.csv`	WTI crude oil futures (`CL=F`)	4,776	`3d1be91bba256bdb30eabac1050ebf2720a079a8d2dfeaa74955df7ca533f54a`
`UST10Y_clean.csv`	10-year Treasury note futures (`ZN=F`)	4,767	`ef788867ed2202c37dbe90cf9f62287f44ff160063d4620a28aa04258957ec94`
`BTC_clean.csv`	Bitcoin (`BTC-USD`), from September 2014	3,391	`79a65ebd28b618b418142267a21e2a55cb23763872418dce04c3f2f527f88529`

The S&P 500 is analysed in daily log losses (%), the other series in simple percentage losses. Rows with a non-positive price are removed, including the WTI settlement of −37.63 on 20 April 2020. [[State whether the price files are included in this repository; Yahoo's terms of use restrict redistribution of its data.]]
Danish fire losses. The 2,167 industrial fire losses above one million DKK (McNeil 1997), 3 January 1980 to 31 December 1990, from the dataset `danish` of the CRAN package `evir`. The download cell fetches the file, checks that it is the paper's sample (number of claims, total loss, first and last date), and saves `data/danish_fire_losses.csv` with its checksum.
Simulated samples. Every simulated sample is saved to `data/simulated/` when generated.

Reproducibility

Random numbers. Every simulated sample and every bootstrap draws from its own random stream, keyed by the master seed (20240601) and a fixed label. Results therefore do not depend on execution order or on the number of parallel processes.
Check against the original results. CELL 16 recomputes stored results of the original study. On the environment below, the largest relative difference is 1.3 × 10⁻⁸.
TMV benchmark. TMV uses a numerical optimiser (SLSQP), whose result depends slightly on the SciPy version. Its entries can differ from the paper by a few tenths of a percentage point (for example 19.0 instead of 19.4 in Table 5). All other entries are reproduced to the precision printed in the paper.
Environment of the full run in `TPWPhd.ipynb`:
```
Python 3.12.7
numpy 1.26.4
pandas 2.2.2
scipy 1.13.1
matplotlib 3.9.2
```
Citation
```bibtex
@article{gwanani_tpw,
  author  = {Gwanani, Isaac and Dhliwayo, Lawrence and Nadarajah, Saralees and Makurumidze, Shepard},
  title   = {Expected Shortfall under threshold uncertainty: target-specific weighting of peaks-over-threshold estimates},
  journal = {Journal of Risk and Financial Management},

}
```
Contact
Isaac Gwanani, University of Zimbabwe, gwananii@gmail.com
