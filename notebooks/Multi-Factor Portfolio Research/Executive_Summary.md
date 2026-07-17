# Executive Summary
## Multi-Factor Portfolio Research Framework — Indian Equities

**Audience:** Portfolio managers, quantitative researchers, quantitative analysts, recruiters and
interviewers evaluating this as a demonstration of institutional research methodology.

**Scope:** A six-notebook research pipeline covering universe construction, factor research,
portfolio construction, walk-forward backtesting, performance analytics, and regime diagnostics,
for a systematic multi-factor Indian equity portfolio (SEBI Large + Mid Cap universe).

---

## 1. Objective

This project evaluates whether a transparent, price/return-based composite factor model can
construct a Top-20 equity portfolio that systematically outperforms relevant benchmarks, and
characterises that outperformance along four dimensions a portfolio manager would ask about:
weighting methodology, rebalancing-frequency sensitivity, transaction-cost impact, and
market-regime stability. It is a methodology demonstration — every design decision is disclosed
and defensible on its own terms, not tuned to produce a favourable backtest.

## 2. Universe and Data

The investable universe is the SEBI Large Cap + Mid Cap classification, published by AMFI and
**dynamically reconstituted every six months** (17 snapshots, 2018-06-30 onward) rather than
fixed at a single point in time — avoiding the survivorship bias a static universe would
introduce, since capitalisation growth is itself correlated with the factors under study
(notably Momentum). Prices are sourced from yfinance for NSE-listed securities only, with a
Corporate Action Registry explicitly tracking every ticker rename, merger, or delisting so that
no ticker is silently dropped. The final research universe comprises 404 tickers and 2,755
trading days of price history; the live backtest window runs 2018-07-01 onward, preceded by a
2015-07-01 warm-up period so the longest factor lookback (756 trading days) is fully populated
at the first rebalance.

## 3. Factor Model

A four-factor composite score, computed entirely from price/return data (no fundamentals
dependency):

| Factor | Lookback | Weight | Role |
|---|---:|---:|---|
| Momentum (12-1 month) | 252 days | 40% | Alpha generation |
| Low Volatility | 252 days | 20% | Risk control |
| Maximum Drawdown | 756 days (~3yr) | 20% | Tail-risk control |
| Beta (closeness to 1.0) | 756 days (~3yr) | 20% | Market-exposure control |

Quality and Value factors were part of the original design but were removed after yfinance's
fundamentals data proved to have structurally poor point-in-time coverage across the backtest
window. Weights (40/20/20/20) were fixed **by design intent before any backtest was run**, never
tuned against historical performance — a deliberate choice to avoid in-sample overfitting. Full
rationale, including a factor-correlation review, is in `docs/design_decisions.md`.

## 4. Portfolio Construction and Backtest

The Top 20 stocks by composite score are selected at each rebalance date, weighted two ways
(Equal Weight; True Equal Risk Contribution via `scipy.optimize`), across three rebalancing
frequencies (Monthly / Quarterly / Semi-Annual) — six combinations total. The walk-forward
backtest allows weights to drift with daily returns between rebalances (no artificial daily
re-weighting), deducts a one-way-turnover × 20 bps transaction cost at each rebalance, and
reports both Gross and Net cumulative return series so cost drag remains visible.

## 5. Headline Results (Net of Costs)

| Combination | CAGR | Ann. Vol | Sharpe | Sortino | Max DD | Calmar |
|---|---:|---:|---:|---:|---:|---:|
| Monthly / EqualWeight | 27.05% | 21.72% | 1.213 | 1.671 | −36.49% | 0.741 |
| Monthly / ERC | 26.46% | 20.93% | 1.228 | 1.690 | −36.23% | 0.730 |
| Quarterly / EqualWeight | 28.58% | 21.65% | 1.271 | 1.779 | −35.41% | 0.807 |
| Quarterly / ERC | 28.06% | 20.86% | 1.291 | 1.808 | −35.08% | 0.800 |
| SemiAnnual / EqualWeight | 28.86% | 21.43% | 1.292 | 1.800 | −35.41% | 0.815 |
| SemiAnnual / ERC | 28.27% | 20.79% | 1.303 | 1.812 | −35.08% | 0.806 |
| NIFTY 50 (benchmark) | 10.94% | 17.32% | 0.687 | 0.945 | −38.44% | 0.285 |
| Universe Avg (benchmark) | 17.19% | 17.78% | 0.982 | 1.310 | −39.23% | 0.438 |

Every one of the six combinations outperforms both benchmarks on every portfolio-level metric
over this backtest window. Against NIFTY 50: annualised Alpha ranges +15.1% to +17.0%, Beta stays
below 1.0 throughout (0.938–0.957), and Up Capture (100.7–103.3%) consistently exceeds Down
Capture (83.0–86.6%) — the portfolios participate more in NIFTY 50's up days than its down days at
lower-than-market systematic exposure. Differences between weighting schemes and frequencies are
modest by comparison; Semi-Annual and Quarterly edge out Monthly on Net CAGR, consistent with
lower cumulative transaction-cost drag at less frequent rebalancing.

## 6. Transaction Cost Sensitivity

Cost drag scales directly with rebalancing frequency: cumulative Gross-minus-Net drag runs from
~38pp (Monthly) down to ~15pp (Semi-Annual) over the full backtest. A closed-form cost-sensitivity
sweep (0/10/20/50 bps), validated three independent ways including a full engine re-run, confirms
every combination's Net return declines monotonically as the cost assumption rises — e.g.,
Quarterly Equal Weight moves from 631.05% (0 bps) to 609.24% (20 bps) to 577.69% (50 bps)
cumulative Net return.

## 7. Rotation Quality — A Qualification to the Headline Result

The Rotation Analysis (baseline combination: Quarterly + Equal Weight) examined 31 stock-swap
events at rebalance and found a 48.4% success rate — only modestly above a coin flip — with a
positive mean Replacement Alpha (+1.93pp) but a **negative median** (−0.65pp): the top 3 rotations
account for 42.0% of all positive replacement alpha. This indicates the value of ongoing Top-20
reconstitution in this backtest owes more to a small number of large successful swaps than to a
broadly consistent edge across most individual rotations — a meaningful qualification the
mean-only success-rate framing would have obscured.

## 8. Factor Attribution — Confirmatory, Not Novel

Comparing the selected Top-20 portfolio's mean factor z-scores against the eligible universe's,
across all 33 baseline rebalance dates, confirms the realised portfolio tilts in the direction the
locked weighting was designed to produce: strongly positive on Momentum (+1.65z) and Maximum
Drawdown (+0.73z), moderately positive on Beta (+0.66z), and negligible on Low Volatility
(−0.05z). This is a descriptive check on the realised selections, not an independent finding.

## 9. Regime Stability

A 3-state Gaussian HMM fit on NIFTY 50 returns alone (never on portfolio returns, never feeding
back into construction) identifies Bull, Bear, and High Volatility regimes with reasonably stable
persistence (only 1.7% of regime runs lasted 3 days or fewer). The baseline combination
outperformed NIFTY 50's annualised return in **every regime present** during the backtest window
— most notably in Bear (+10.0% vs. −0.12%) — indicating the headline outperformance is not an
artifact of favourable performance in only one type of market environment.

## 10. Limitations

- Survivorship bias between semi-annual AMFI snapshots; NSE-only listings.
- No Quality or Value factors, due to yfinance's limited point-in-time fundamentals coverage.
- Transaction costs modelled as turnover × bps only — no market impact, spread, or slippage.
- Rotation Analysis's Replacement Alpha definition is disclosed but not a formally locked project
  convention; the ±0.05z tilt-reporting threshold in Factor Attribution is similarly a reporting
  choice.
- Regime labels are a modelling choice (3 features, 3 states, 21-day volatility window), not
  ground truth; the overlay is observational and non-causal.
- All results describe this specific historical backtest window and are not a forward-looking
  performance guarantee.

## 11. Conclusion

Across every combination tested, the composite-factor Top-20 portfolio outperformed both
benchmarks over the backtest window on every risk-adjusted metric reported, with the
outperformance holding across rebalancing frequencies, weighting schemes, and market regimes. The
rotation and factor-attribution diagnostics qualify — rather than simply restate — that headline
result: outperformance is real but partly concentrated in a handful of large successful
rotations, and it verifiably arises from the intended factor tilts rather than an unexplained
residual. Full detail is documented per-notebook in the notebooks themselves and summarised in
`docs/key_findings.md` and `docs/methodology.md`.
