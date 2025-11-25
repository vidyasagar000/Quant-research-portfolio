# GARCH(1,1) Volatility Forecasting on Real Market Data

**Notebook:** `GARCH_RealData.ipynb`

## Summary
This project models daily return volatility using GARCH(1,1) and compares the effect of using GARCH-forecasted volatility vs constant sample volatility in option pricing.

## What is included
- Robust price fetching helper using `yfinance` (handles MultiIndex and missing Adj Close)
- Calculation of log returns and sample annualized volatility
- Fitting GARCH(1,1) (and variants like EGARCH/GJR optionally) with `arch`
- One-step and multi-step volatility forecasts
- Re-pricing example: use forecast sigma in Monte Carlo/BS approximation and compare

## How to run
1. Install dependencies: `pip install -r requirements.txt`  
2. In notebook top cell specify `ticker`, `period` (e.g., `5y`), and `train_window`  
3. Run top-to-bottom. Default GARCH fit uses `arch` library.

## Key functions / cells
- `get_close_series(ticker, period='5y')` — robust fetcher
- Data cleaning & returns calculation
- GARCH fit cell: parameter estimation and diagnostics
- Forecast cell: compute the 1-day and annualized sigma
- Comparison cell: Monte Carlo/BS using sample sigma vs GARCH sigma

## Outputs
- Time series plots: price, daily returns, realized volatility (rolling)
- GARCH conditional volatility time series
- Table: sample sigma vs GARCH sigma (annualized)
- Option price comparisons

## Notes & caveats
- If `arch` is not installed: `pip install arch`
- The permutation/bootstrap methods are outside this notebook — see CrossCorrelation project for significance tests.
- Be cautious: forecasts are model-dependent and should be interpreted as conditional estimates, not ground truth.

