# Assumptions

Every assumption underlying this project, in one place. Where an assumption is a locked project
convention (applies identically across all six notebooks), that is noted; where it is
notebook-specific, the source notebook is named.

## Universe and Data

- **NSE-listed securities only** (`.NS` suffix); BSE/MSEI-only listings are out of scope. (NB01)
- **Prices are dividend/split-adjusted close** (`yfinance`, `auto_adjust=True`). (NB01)
- **Universe membership is dynamic**, re-evaluated at each AMFI snapshot (semi-annual), not fixed
  at a single point in time. (NB01)
- **Point-in-time coverage/liquidity is a per-rebalance-date question**, resolved at the rebalance
  level (NB03), not filtered globally in NB01.
- **252 trading days per year** for all annualisation. (Project-wide)
- **Warm-up period starts 2015-07-01**; live backtest window starts 2018-07-01. (NB01)

## Factor Model

- **All four factors are computed using only price/return data up to and including each
  rebalance date** — none can observe the future. Trailing windows are position-based against the
  price panel's own trading-day index, not calendar-day arithmetic. (NB02)
- **Factor weights (40/20/20/20) were set from financial intuition and common
  quantitative-portfolio-management practice**, before any backtest was run — never tuned against
  historical performance. (NB02)
- **Winsorisation clip at ±3.0 z-score** for every factor, at every rebalance date. (NB02)
- **A stock is eligible for ranking only if all four factor z-scores are computable** that date.
  (NB02)

## Portfolio Construction

- **Target portfolio size: Top 20 by composite score.** (NB03)
- **Long-only, fully invested, no leverage** ($w_i \in [0,1]$, $\sum_i w_i = 1$). (NB03)
- **Position sizing is expressed as continuous portfolio weights** — no share-count/lot-size
  rounding constraint is modelled. (NB03)
- **ERC minimum observation threshold: 100 daily returns**; below this, the optimizer falls back
  to Equal Weight for that (date, frequency) combination, logged rather than silently applied.
  (NB03)
- **Covariance matrices with condition number above $10^6$ are shrunk 10% toward their diagonal**
  before ERC optimisation. (NB03)
- **No additional liquidity filter** beyond the factor panel's own history requirement. (NB03)

## Backtest Mechanics

- **No starting capital, no "portfolio value" language anywhere** — every series is a compounded
  return series, $\prod_t(1+r_t)$, with first value 1.0 as a mathematical normalisation only.
  (Project-wide)
- **Transaction cost: one-way turnover × `DEFAULT_COST_BPS` (default 20bps)**, buy and sell legs
  combined into a single cost, not doubled. (NB04)
- **No market impact, bid-ask spread, or slippage model** — transaction costs are turnover × basis
  points only. (NB04)
- **No taxes.** Not modelled anywhere in this project.
- **Weights drift with daily returns between rebalances** (no artificial daily re-weighting); the
  rebalance-date return itself is realised using pre-rebalance drifted weights, with the cost for
  moving to new target weights deducted the same day. (NB04)
- **Missing daily returns for a held stock are treated as a flat 0% return** for that stock that
  day, logged as a diagnostic. (NB04)

## Performance Metrics

- **Risk-free rate fixed at 0%** throughout, applied to Sharpe/Sortino. (Project-wide, used from
  NB05 onward)
- **Degenerate-input edge cases** (zero volatility, zero benchmark variance, checked within a
  $10^{-10}$ tolerance) return `NaN`, never `inf` or a silent crash. (NB05)
- **Baseline combination for single-combination diagnostics** (sector analytics, Rotation
  Analysis, Factor Attribution, Regime overlay): Quarterly + Equal Weight. (NB05, NB06)
- **Transaction-cost sensitivity swept at 0/10/20/50 bps**, validated via closed-form and full
  engine re-run agreement. (NB05)

## Regime Diagnostics

- **3-state Gaussian HMM**, `covariance_type='diag'` (independent per-feature variances, no
  cross-covariance terms) — the simpler, less overfitting-prone choice for a 3-feature, 3-state
  model on a few thousand observations. (NB06)
- **A fixed random seed** for EM initialisation, since HMM fitting is otherwise sensitive to its
  random starting point. (NB06)
- **Rolling volatility window: 21 trading days** (~1 month) as one of three HMM input features.
  (NB06)
- **A regime run of 3 days or fewer is flagged as a potentially unstable state assignment.** (NB06)
- **HMM fit on NIFTY 50 returns only** — never on portfolio returns, and never used as a
  portfolio-construction input anywhere in this project. (NB06)

## Testing Discipline

- **Every numerically consequential formula is verified against a hand-computed worked example**
  before being trusted for the full dataset (walk-forward compounding engine, performance
  metrics, regime-conditional aggregation).
- Because the assistant's development environment had no live network access to `yfinance.com` or
  `amfiindia.com`, notebooks were validated via `ast.parse` syntax checking of every cell, a
  synthetic-data test harness executing every cell in-process, and hand-computed unit tests for
  numerically consequential logic — not via a live end-to-end run in that environment. (The
  project owner has since run all six notebooks live, producing the actual figures reported in
  `key_findings.md`.)
