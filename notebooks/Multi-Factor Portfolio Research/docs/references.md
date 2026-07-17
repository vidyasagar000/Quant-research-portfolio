# References

Methodological references underlying the design choices in this project. This is not an
exhaustive academic literature review — it documents the specific conventions this project's
methodology draws on or is consistent with, as noted in the notebooks themselves.

## Factor Methodology

- **Momentum (12-1 month specification):** Jegadeesh, N., & Titman, S. (1993). *Returns to Buying
  Winners and Selling Losers: Implications for Stock Market Efficiency.* The Journal of Finance,
  48(1), 65–91. This project's 252-day lookback with a 21-day skip follows this specification
  directly.
- **Low Volatility factor window convention:** consistent with institutional low-volatility index
  methodologies using a trailing 1-year daily-volatility window (e.g., the S&P Low Volatility
  Index methodology).
- **Beta estimation window convention:** consistent with vendor conventions preferring multi-year
  windows for beta estimation stability (e.g., Bloomberg's standard 2-year-weekly-data beta) —
  different sampling frequency from this project's 756-day daily window, but the same underlying
  preference for reduced estimation noise over a single-year window.

## Portfolio Construction

- **Equal Risk Contribution (ERC) / Risk Parity:** Maillard, S., Roncalli, T., & Teïletche, J.
  (2010). *The Properties of Equally Weighted Risk Contribution Portfolios.* The Journal of
  Portfolio Management, 36(4), 60–70. This project's ERC objective (minimising dispersion of
  per-stock risk contributions around an equal split) follows this framework.

## Regime Modelling

- **Hidden Markov Models for regime detection:** the general approach of fitting a Gaussian HMM to
  return/volatility/drawdown features to identify latent market regimes is a standard technique in
  quantitative regime-detection literature; this project's specific implementation (3 states,
  diagonal covariance, standardised features) is a disclosed, project-specific configuration
  rather than a reproduction of any single published study.

## Data Sources

- **AMFI (Association of Mutual Funds in India):** stock categorisation (Large Cap / Mid Cap
  classification), published semi-annually — https://www.amfiindia.com
- **NSE (National Stock Exchange of India):** underlying listing venue for all securities in this
  project's universe (`.NS` ticker suffix).
- **yfinance:** Python library used for daily price/volume history and index data (NIFTY 50).
- **NIFTY 50:** primary market benchmark, India's headline large-cap equity index.

## Software

- **hmmlearn:** Gaussian Hidden Markov Model implementation used in NB06.
- **scipy.optimize:** constrained optimisation solver used for the True ERC portfolio weights in
  NB03.
- **pandas / numpy / matplotlib:** core data manipulation, numerical computation, and
  visualisation throughout all six notebooks.

## Project-Internal Documentation

- `docs/methodology.md` — full formulas as actually implemented.
- `docs/design_decisions.md` — rationale for every parameter choice referenced above.
