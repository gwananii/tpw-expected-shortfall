# v1.0.0 — Replication package for "Threshold-Averaged Expected Shortfall Under Weak Identification"

Code and stored results for the paper *Threshold-Averaged Expected Shortfall Under Weak Identification*
by Isaac Gwanani, Lawrence Dhliwayo, Saralees Nadarajah and Shepard Makurumidze, submitted to *Risks*
(MDPI), manuscript risks-4730816.

This release contains:

- the analysis code, which reproduces every table and figure of the paper (Steps 1–14);
- `r_results_and_versions.zip`: the stored results of every R computation of the reported run
  (Bayesian threshold averaging, ForwardStop and EQD), the two R scripts and `software_versions.txt`;
- `tables_full.zip`: every result table of the reported full run, as CSV;
- `requirements.txt` and a README that maps each table and figure of the paper to its output file.

The stored R results reproduce the reported Bayesian and EQD numbers exactly; rerunning R gives
slightly different values because these methods are Monte Carlo based (Section 4.5 of the paper).
Market price snapshots from Yahoo Finance are not redistributed; the code downloads them and checks
them against the SHA-256 checksums of the reported run.

The repository was first described under an earlier working title; the paper's title is now final.
The code and results are unchanged by the change of title.
