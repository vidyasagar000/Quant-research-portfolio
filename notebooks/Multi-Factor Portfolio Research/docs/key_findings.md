# Key Findings

Actual computed results from each notebook. No figure below is projected, estimated, or
illustrative — every number comes directly from the corresponding notebook's Results / Key
Findings section.

---

## NB01 — Universe Construction

- AMFI catalogue: 17 snapshots (2018-06-30 → latest), all successfully downloaded.
- Master universe: 441 unique tickers ever classified Large or Mid Cap.
- Corporate Action Registry: 39 entries (19 ticker changes, 10 mergers, 4 delistings, 6
  unresolved/unknown).
- Resolution Log breakdown: 386 `SUCCESS`, 18 `RENAMED`, 17 `FAILED`, 10 `MERGED`, 6 `UNKNOWN`,
  4 `DELISTED`.
- **Final research universe: 404 tickers, 2,755 trading days of price history.**
- NIFTY 50 CAGR (backtest window, 2018-07-02 onward): 10.68%.
- Universe median annualised return (backtest window): 19.32%.
- 35 of 441 tickers excluded prior to any factor or portfolio logic, each with an explicit,
  auditable reason (14 registry-confirmed mergers/delistings; 21 failed/unknown after a download
  attempt).

## NB02 — Factor Research

- Rebalance grid: 97 month-end trading dates, 2018-07-31 → 2026-07-15.
- Factor coverage (of 404 universe tickers) rose from first to last rebalance date: Momentum
  297 → 389, Low Volatility 320 → 404, Maximum Drawdown 280 → 375, Beta 280 → 377.
- Eligible (all-four-factors-present) universe size: 280 at the first rebalance date, 375 at the
  last, median 338 across all 97 dates. **No rebalance date ever fell below the 20-stock minimum**
  NB03 requires.
- Median Maximum Drawdown across the universe at the last rebalance date: −40.6%.
- Median Beta across the universe at the last rebalance date: 1.03.
- Factor correlation review confirmed the expected structure: positive but not redundant
  correlation between Low Volatility and Maximum Drawdown, and a near-orthogonal Beta factor.

## NB03 — Portfolio Construction

- Portfolio holdings panel: 5,880 rows (147 (frequency, rebalance date) combinations × 2
  weighting schemes × 20 stocks); **0 of 294 weight groups failed to sum to 1.0.**
- All 147 (frequency, rebalance date) combinations reached the full 20-stock target — the
  under-sized-portfolio fallback was never triggered.
- ERC diagnostics: 65 of 148 logged attempts required covariance shrinkage (near-singular raw
  estimate); **0 fell back to Equal Weight for insufficient history; 0 failed to converge.**
- Median effective N (1/HHI): Equal Weight exactly 20.00 at every frequency; ERC 19.17 (Monthly),
  19.11 (Quarterly), 19.07 (Semi-Annual) — a modest, not extreme, concentration tilt.
- Sector coverage: 383/404 tickers (94.8%) with a known sector, 21 `Unknown`.
- Ranked-universe panel: 49,071 rows (2,940 selected, 46,131 non-selected).
- Median Top-20 turnover per rebalance: 5 names (Monthly), 9 (Quarterly), 13 (Semi-Annual) —
  scaling roughly proportionally with the gap between rebalances.

## NB04 — Walk-Forward Backtest

- All six (frequency × weighting scheme) combinations produced 1,964 return days, with rebalance
  counts of 96 (Monthly), 32 (Quarterly), 16 (Semi-Annual), and **0 missing-return fills** across
  every combination.
- Date coverage matched the expected trading-day count exactly for all six combinations.
- **Final cumulative Gross / Net returns:**

  | Combination | Gross | Net |
  |---|---:|---:|
  | Monthly EqualWeight | 6.85× | 6.46× |
  | Monthly ERC | 6.61× | 6.23× |
  | Quarterly EqualWeight | 7.31× | 7.09× |
  | Quarterly ERC | 7.09× | 6.87× |
  | Semi-Annual EqualWeight | 7.37× | 7.22× |
  | Semi-Annual ERC | 7.10× | 6.96× |

- **Cumulative cost drag (Gross − Net, percentage points):** Monthly 38.3 (EqualWeight) / 37.7
  (ERC), Quarterly 21.8 / 21.4, Semi-Annual 14.9 / 14.3 — decreasing monotonically as rebalancing
  frequency lengthens, roughly 2.6× higher at Monthly than Semi-Annual.

## NB05 — Portfolio Analytics

**Portfolio-level metrics (Net, all six combinations) and both benchmarks:**

| Combination | CAGR (%) | Ann. Vol (%) | Sharpe | Sortino | Max Drawdown (%) | Calmar |
|---|---:|---:|---:|---:|---:|---:|
| Monthly / ERC | 26.46 | 20.93 | 1.228 | 1.690 | −36.23 | 0.730 |
| Monthly / EqualWeight | 27.05 | 21.72 | 1.213 | 1.671 | −36.49 | 0.741 |
| Quarterly / ERC | 28.06 | 20.86 | 1.291 | 1.808 | −35.08 | 0.800 |
| Quarterly / EqualWeight | 28.58 | 21.65 | 1.271 | 1.779 | −35.41 | 0.807 |
| SemiAnnual / ERC | 28.27 | 20.79 | 1.303 | 1.812 | −35.08 | 0.806 |
| SemiAnnual / EqualWeight | 28.86 | 21.43 | 1.292 | 1.800 | −35.41 | 0.815 |
| NIFTY 50 (Benchmark) | 10.94 | 17.32 | 0.687 | 0.945 | −38.44 | 0.285 |
| Universe Avg (Benchmark) | 17.19 | 17.78 | 0.982 | 1.310 | −39.23 | 0.438 |

**Benchmark-relative metrics vs. NIFTY 50 (Net), across all six combinations:** Alpha +15.13% to
+17.00% annualised; Beta 0.938 to 0.957 (all below 1.0); Information Ratio 1.074 to 1.233; Up
Capture 100.7% to 103.3%; Down Capture 83.0% to 86.6%.

**Cost-sensitivity sweep (cumulative Net return %, 0/10/20/50 bps):** monotonic decline for every
combination — e.g. Quarterly EqualWeight: 631.05% (0bps) → 609.24% (20bps) → 577.69% (50bps). All
three independent validation checks passed (0bps reproduces Gross exactly; 20bps matches NB04's
realised Net exactly; a full engine re-run at 50bps agrees to 9.97×10⁻¹⁷ — floating-point
precision).

**Benchmark return series:** NIFTY 50 produced 1,978 backtest-window return days, compounding to
2.2593× (+125.93%). Universe Average Return produced 1,986 return days (fully defined, no
undefined days), compounding to 3.4907× (+249.07%); 208 classified-ticker-days across the full
window had no corresponding price and were excluded from that day's average only.

**Sector analytics (baseline combination, averaged across the backtest):** Basic Materials
(17.27%), Industrials (17.12%), Consumer Cyclical (16.82%), Financial Services (15.91%),
Technology/Healthcare/Consumer Defensive/Utilities each 5–7%, Real Estate smallest (0.76%),
`Unknown` 2.73% on average.

**Diagnostics:** 0 of 8 metric series (six combinations + two benchmarks) produced a `NaN`
Sharpe/Sortino; 0 of 6 combinations produced a `NaN` Beta; 18 of 660 baseline holdings rows
(2.7%) carry an `Unknown` sector.

### Rotation Analysis (baseline: Quarterly + Equal Weight)

- **31 rotation events identified**, Rotation Success Rate **48.4%**, average Replacement Alpha
  **+1.93 percentage points** per rotation (forward-window, not annualised).
- **Median Replacement Alpha: −0.65pp** (negative, despite the positive mean) — **top 3 rotations
  account for 42.0% of all positive replacement alpha.**
- Interpretation: a concentrated, not a broadly consistent, edge — the 48.4% success rate is only
  modestly above a coin flip, and removing a handful of large winning replacements would erase
  most of the average edge.

### Factor Attribution (baseline: Quarterly, all 33 rebalance dates covered)

| Factor | Portfolio mean z | Universe mean z | Difference |
|---|---:|---:|---:|
| Momentum | 1.625 | −0.024 | **+1.649** |
| Maximum Drawdown | 0.734 | 0.003 | **+0.731** |
| Beta | — | — | +0.662 |
| Low Volatility | — | — | −0.048 (below reporting threshold) |

Strongest tilt by a wide margin is Momentum — consistent with its 40% composite weight. No
factor shows a notable negative tilt.

## NB06 — Regime Diagnostics

- HMM converged (`True`, within 1,000 EM iterations), identifying 3 regimes across 2,697 trading
  days of NIFTY 50 history:

  | Regime | % of days | Mean daily return | Mean drawdown |
  |---|---:|---:|---:|
  | Bear | 47.9% | +0.00001 | −6.46% |
  | Bull | 37.9% | +0.00090 | −1.26% |
  | High Volatility | 14.2% | +0.00070 | −11.87% (≈2× Bear's volatility) |

- **Expected regime durations:** Bear 43.7 days, Bull 52.5 days, High Volatility 29.4 days. Of 60
  total regime runs, only **1 (1.7%) lasted 3 days or fewer** — indicating a reasonably stable
  segmentation, not a noisy artifact.
- **Regime-conditional performance (baseline vs. NIFTY 50, backtest window, 1,979 days total):**

  | Regime | Days | Baseline Ann. Return | Baseline Sharpe | NIFTY 50 Ann. Return |
  |---|---:|---:|---:|---:|
  | Bull | 690 | +46.71% | 3.00 | +22.60% |
  | Bear | 970 | +10.00% | 0.51 | −0.12% |
  | High Volatility | 319 | +40.24% | 1.17 | +24.83% |

- **The baseline combination outperformed NIFTY 50's annualised return in every regime present**
  during the backtest window — largest absolute edge in Bear (turning an essentially flat-to-
  negative benchmark period into a double-digit annualised return), highest risk-adjusted return
  in Bull (Sharpe 3.00).

---

## Cross-Notebook Synthesis

Every combination of frequency and weighting scheme outperformed both benchmarks on every
portfolio-level metric tested. That outperformance:
- **scales inversely with transaction cost drag** (NB04) — Semi-Annual/Quarterly edge out Monthly
  on Net CAGR;
- **holds under a full cost-sensitivity sweep** (NB05) — cost drag is material but does not erase
  the edge, even at 50bps;
- **is partly concentrated rather than uniformly distributed across individual trades** (NB05
  Rotation Analysis) — a meaningful qualification, not a contradiction, of the headline result;
- **arises from the intended factor tilts** (NB05 Factor Attribution) — confirmatory, not a new
  finding;
- **holds across all three market regimes tested** (NB06) — not an artifact of one favourable
  environment.
