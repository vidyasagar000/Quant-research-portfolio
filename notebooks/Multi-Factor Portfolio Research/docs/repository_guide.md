# Repository Guide

How to navigate, run, and reproduce this project.

## Folder Structure

```
Multi-Factor Portfolio Research/
├── notebooks/
│   ├── NB01_Universe_Construction.ipynb
│   ├── NB02_Factor_Research.ipynb
│   ├── NB03_Portfolio_Construction.ipynb
│   ├── NB04_WalkForward_Backtest.ipynb
│   ├── NB05_Portfolio_Analytics.ipynb
│   └── NB06_Regime_Diagnostics.ipynb
├── data/          # generated on run — CSV/JSON artifacts, chained across notebooks
├── output/        # generated on run — chart/table PNGs, dark-themed
├── docs/
│   ├── methodology.md
│   ├── design_decisions.md
│   ├── assumptions.md
│   ├── key_findings.md
│   ├── repository_guide.md   # this file
│   └── references.md
├── Executive_Summary.md
└── README.md
```

Notebooks chain **only through `data/`** — there is no direct notebook-to-notebook import.
Each notebook loads specific CSV/JSON files that an earlier notebook wrote and, on completion,
writes its own outputs for the next notebook to consume.

## Run Order and Dependency Map

| Notebook | Reads from `data/` | Writes to `data/` |
|---|---|---|
| **NB01** Universe Construction | — (external: AMFI, yfinance) | `amfi_membership_panel.csv`, `amfi_membership_matrix.csv`, `master_universe.csv`, `raw_prices.csv`, `raw_volumes.csv`, `daily_returns.csv`, `log_returns.csv`, `benchmark_nifty50.csv`, `ticker_company_map.csv`, `ticker_resolution_log.csv`, `data_quality_report.csv`, `study_window.json` |
| **NB02** Factor Research | NB01 outputs above | `factor_panel.csv`, rebalance date grid, eligible-universe-size series |
| **NB03** Portfolio Construction | NB01 + NB02 outputs | `portfolio_holdings.csv`, `portfolio_selection_log.csv`, `erc_diagnostics_log.csv`, `portfolio_concentration.csv`, `ticker_sector_map.csv`, `ranked_universe_panel.csv`, `portfolio_turnover_log.csv`, `selection_frequency.csv`, `sector_allocation.csv`, `capclass_allocation.csv` |
| **NB04** Walk-Forward Backtest | NB01 (`daily_returns.csv`) + NB03 (`portfolio_holdings.csv`, `study_window.json`) | `backtest_daily_returns.csv`, `backtest_cumulative_returns.csv`, `backtest_turnover_cost_log.csv`, `backtest_diagnostics.csv`, `backtest_summary.csv` |
| **NB05** Portfolio Analytics | NB01, NB02 (`factor_panel.csv`), NB03 (`ticker_sector_map.csv`, `portfolio_holdings.csv`), NB04 (all backtest outputs) | `performance_metrics_portfolio.csv`, `performance_metrics_benchmark_relative.csv`, `cost_sensitivity_sweep.csv`, `sector_weight_over_time_baseline.csv`, `universe_average_return.csv`, `nifty50_daily_return_backtest_window.csv`, `rotation_analysis.csv`, `factor_attribution_summary.csv`, `factor_attribution_by_date.csv` |
| **NB06** Regime Diagnostics | NB01 (`benchmark_nifty50.csv`, `study_window.json`), NB04 (`backtest_daily_returns.csv`, filtered to baseline) | `regime_timeline.csv`, `regime_transition_matrix.csv`, `regime_performance_summary.csv`, `regime_runs.csv` |

**Run notebooks strictly in order, NB01 → NB06.** Each expects the previous notebook's `data/`
outputs to already exist.

## Requirements

- Python environment with: `pandas`, `numpy`, `scipy`, `matplotlib`, `yfinance`, `hmmlearn`,
  `openpyxl` (for AMFI's Excel snapshots).
- **Live network access** to `amfiindia.com` (AMFI classification downloads) and Yahoo Finance's
  data endpoints (via `yfinance`) is required for NB01. Once `data/` is populated, NB02–NB06 run
  offline against those cached CSVs.
- AMFI occasionally rate-limits or renames source files; NB01 includes a manual-fallback path
  (`data/amfi_raw/`) for pre-downloaded snapshot files if the live download fails.

## What Each Notebook Actually Does

- **NB01 — Universe Construction.** Builds the dynamic AMFI Large+Mid Cap universe, resolves
  every ticker's corporate-action status (renamed/merged/delisted/failed/unknown), downloads and
  cleans price/volume history, computes simple and log returns.
- **NB02 — Factor Research.** Computes the four-factor composite score (Momentum, Low Volatility,
  Maximum Drawdown, Beta) at every month-end rebalance date, point-in-time safe by construction.
- **NB03 — Portfolio Construction.** Selects the Top 20 by composite score at each rebalance date
  and frequency; computes Equal Weight and True ERC portfolio weights; includes a Portfolio
  Research section (sector/cap-class allocation, turnover, buffer/watchlist, selection frequency).
- **NB04 — Walk-Forward Backtest.** Compounds NB03's weights into daily Gross/Net returns under
  realistic weight drift and transaction costs, for all six (frequency × scheme) combinations.
- **NB05 — Portfolio Analytics.** Computes risk-adjusted performance metrics, benchmark-relative
  metrics (vs. NIFTY 50), the Universe Average Return benchmark, a transaction-cost sensitivity
  sweep, sector analytics, Rotation Analysis, and Factor Attribution.
- **NB06 — Regime Diagnostics.** Fits a 3-state Gaussian HMM on NIFTY 50 returns and overlays the
  baseline combination's realised performance by regime — the final notebook in the project.

## Recommended Reading Order for a Reviewer

1. `README.md` — orientation and headline results.
2. `Executive_Summary.md` — condensed narrative of the full project.
3. `docs/methodology.md` — formulas and computational methodology.
4. `docs/design_decisions.md` — why each locked choice was made.
5. Notebooks themselves, NB01 → NB06, for full detail and code.
6. `docs/key_findings.md` — actual results, for verification against the notebooks.

## Reproducing Results

Because the universe is dynamically reconstituted from live AMFI snapshots and prices are pulled
from yfinance at run time, exact figures may drift slightly on re-run (new AMFI snapshots
published since, corporate actions since resolved differently, yfinance data revisions). The
figures in `docs/key_findings.md` reflect one specific completed run; the *methodology* — not
the exact numeric output — is what this project intends to be reproducible.

## Notes for Contributors / Future Work

Documented, out-of-scope extensions from `docs/design_decisions.md`:
- Statistically-driven factor weighting (risk parity across factors, PCA-based effective-number-
  of-factors weighting) as an alternative to the locked 40/20/20/20 split.
- A formally locked Replacement Alpha definition for Rotation Analysis (currently disclosed but
  not locked as a project convention).
- Extending Rotation Analysis, Factor Attribution, and the Regime overlay beyond the baseline
  (Quarterly + Equal Weight) combination to all six.
