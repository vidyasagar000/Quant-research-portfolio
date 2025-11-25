# Cross-Asset Lead–Lag Correlation Analysis

**Notebook:** `CrossCorrelation.ipynb`

## Summary
This project adapts time-series correlation methods (inspired by the Z-transformed Discrete Correlation Function used in astrophysics) to detect lead–lag relationships across financial assets. It uses permutation-based testing to assess significance and rolling-window diagnostics to show time-varying correlation.

## What is included
- Robust data fetching (yfinance helper)
- Log-return calculation and alignment of two series
- Cross-correlation computed for lags in a range (e.g., ±20 days)
- Permutation test to compute empirical p-values for maximum correlation peaks
- Rolling-window correlation plot and interpretation helper

## How to run
1. Install dependencies: `pip install -r requirements.txt`  
2. Set the two `tickers` and `period` at the top (e.g., `'RELIANCE.NS'`, `'^NSEI'`)  
3. Default `lags = 20`, `n_perm = 2000` (safe demo defaults); increase `n_perm` for stronger p-value precision
4. Run top-to-bottom in Colab/Jupyter

## Key cells / parameters
- Preprocessing: align date indexes and compute log-returns
- `cross_corr_simple(series_a, series_b, max_lag)` — compute cross-corrs
- Permutation test cell: `n_perm` permutations (shuffle returns) and compute empirical p-value
- Plot cells: price + returns, correlation stem plot, rolling correlation

## Outputs
- Prices & log-returns plots
- Cross-correlation vs lag plot (positive = A leads B)
- Rolling correlation (60-day or user-specified window)
- Top-k correlation peaks and empirical p-value

## Notes, limitations & reproducibility
- The permutation test uses random shuffling of returns. For series with strong autocorrelation consider block-permutation or block-bootstrap (not implemented here).
- Default `n_perm = 2000` is a good trade-off between speed and precision for demos. Increase for production-level testing.
- Use `np.random.seed(2025)` in the notebook for reproducible permutation results.

