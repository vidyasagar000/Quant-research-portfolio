# Monte Carlo Option Pricing (GBM)

**Notebook:** `MonteCarlo_GBM.ipynb`

## Summary
This project implements Monte Carlo simulation to price European call options under a Geometric Brownian Motion (GBM) model and compares the simulated price to the Black–Scholes analytical solution.

## What is included
- Monte Carlo simulation engine with vectorized path generation
- Confidence intervals and Monte Carlo standard error calculations
- Comparison with Black–Scholes price
- Parameter sensitivity analysis (volatility, time-to-maturity, number of paths)

## How to run
1. Install dependencies: `pip install -r requirements.txt`
2. Open `MonteCarlo_GBM.ipynb` in Colab or Jupyter.
3. Default safe parameters (demo mode): `n_paths = 100_000`, `n_steps = 252`.
4. To reproduce results, set `np.random.seed(2025)` at top of notebook.

## Useful cells / parameters
- Top cell: input parameters (`S0`, `K`, `r`, `sigma`, `T`, `n_paths`, `n_steps`)
- Simulation cell: vectorized GBM generation and discounted payoff computation
- Diagnostics cell: Monte Carlo SE and 95% CI
- Timing cell: prints runtime for current `n_paths`

## Outputs
- Histogram of terminal price `S_T`
- Distribution of payoff `(S_T - K)+`
- Monte Carlo convergence plot (price vs number of paths)
- Comparison table: MC price ± CI vs Black–Scholes

## Notes & limitations
- For quick demo use `n_paths=1e5`. For highly accurate runs, increase to `1e6` but expect longer runtime.
- No external data required.
