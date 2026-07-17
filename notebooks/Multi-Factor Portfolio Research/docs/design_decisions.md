# Design Decisions

The rationale behind every locked design choice in this project, organised by notebook. These
decisions were made before backtesting, not tuned against results, and are documented here so
they are explainable on their own terms rather than left implicit in code.

---

## 1. Universe (NB01)

**Dynamic reconstitution, not a static universe.** A static universe (one point-in-time list,
frozen) excludes any stock that migrates into Large/Mid Cap after the study begins and retains
stocks that later fall out of scope — introducing a selection bias correlated with the factors
under study (e.g., momentum-driven capitalisation growth). The universe is instead reconstituted
from 17 semi-annual AMFI snapshots, deferring point-in-time eligibility to each individual
rebalance date rather than resolving it once, globally.

**Coverage/liquidity filtering deferred to NB03, not applied in NB01.** Because the universe is
dynamic, "does this stock have enough history/liquidity right now" is a per-rebalance question.
Applying a single global filter in NB01 — as a static-universe pipeline would — could incorrectly
retain or drop a stock across periods where its eligibility genuinely changed.

**Warm-up period is 2015-07-01, not 2017-07-01.** The 756-trading-day (~3yr) Maximum Drawdown and
Beta lookbacks need a full 3-year price history available at the first backtest rebalance
(2018-07-01). This only extends the historical price pull; `BACKTEST_START` itself is unchanged.

**First usable AMFI snapshot is 2018-06-30.** AMFI's site mislabels the "Jul–Dec 2017" download
(it points to the same file as "Jan–June 2018"). Rather than use corrupted data silently, the
catalogue starts at the first genuinely distinct, correct snapshot — which is why the backtest
begins July 2018.

**NSE-only.** BSE/MSEI-only listings (no NSE symbol) are out of scope; disclosed as a limitation,
not treated as immaterial.

## 2. Factor Model (NB02)

**Why not Quality or Value (v1 → v2 revision).** The original design included Quality
(ROE/ROA/Margin) and Value (P/E, P/B, EV/EBITDA), both requiring point-in-time company
fundamentals. In practice, yfinance's quarterly financials expose only a limited trailing window
(typically the last 4–8 reported quarters as of the query date), producing structurally poor
factor coverage for most of the backtest window. Removed intentionally, not silently dropped —
replaced with Maximum Drawdown and Beta, both computed purely from price/return data, removing
the fundamentals dependency entirely.

**Lookback windows, factor by factor:**
- **Momentum (252d, 21-day skip):** the Jegadeesh–Titman 12-1 specification — the most widely
  validated momentum construction in the literature. Not revisited.
- **Low Volatility (252d), kept deliberately shorter than Maximum Drawdown/Beta.** Matches
  institutional convention (e.g., the S&P Low Volatility Index methodology's 1-year window).
  Keeping Low Volatility short and Maximum Drawdown long makes them measure genuinely different
  things — recent choppiness versus full-cycle severity — rather than the same risk dimension
  restated over an identical period.
- **Maximum Drawdown (756d, ~3yr).** A 1-year Maximum Drawdown is not a tail-risk measure but
  volatility restated, since most 1-year windows do not contain an actual crisis. 756 days is the
  practical floor for a realistic chance of capturing a genuine tail event, balanced against
  warm-up/data-availability cost. A 5-year (1,260-day) window was considered and rejected: it
  would push the warm-up period back further, reducing early-period data availability for newer
  listings precisely when the eligible universe is already thinnest, without a clearly justified
  expected improvement.
- **Beta (756d, ~3yr).** Rationale here is estimation noise, not tail risk: a beta regression on
  252 daily points has materially higher standard error than one on 756. Since this factor's role
  is market-exposure control (a stability objective, not an alpha-timing one), a noisy,
  rebalance-to-rebalance jumpy beta works against its purpose. Broadly consistent with vendor
  conventions such as Bloomberg's standard beta (2 years of weekly data) — different sampling
  frequency, same underlying preference for a multi-year estimation window.

**Beta is scored by distance from 1.0, not minimised.** `beta_distance = -|beta - 1.0|`, then
z-scored like every other factor, so higher always means "better" uniformly across all four
factors. The role of this factor is exposure *control*, not exposure minimisation.

**No orthogonalisation or residualisation.** The factor correlation review found a meaningfully
positive correlation between Low Volatility and Maximum Drawdown even after differentiating their
windows — an economically expected outcome (expected worst-case drawdown scales with volatility
for any return process), not a methodology flaw. Momentum's positive correlation with Maximum
Drawdown is similarly expected (recent winners mechanically tend to show shallower recent
drawdowns). Statistical decorrelation techniques (orthogonalising against Low Volatility,
PCA-based recombination) were considered and deliberately not applied — priority was given to
keeping each factor definition intuitive and independently explainable over marginal gains in
statistical orthogonality. Beta's near-zero correlation with every other factor was the cleanest,
most orthogonal result in the set.

**Weighting policy: 40/20/20/20, locked by design intent, never tuned.** Momentum is deliberately
the primary alpha-generating factor; Low Volatility, Maximum Drawdown, and Beta are deliberately
complementary risk-control factors, weighted equally to each other. Not derived from any
statistical procedure (risk parity across factors, PCA-based effective-factor weighting,
inverse-correlation weighting) and not tuned against backtest performance — set ex-ante from
portfolio-construction intent, specifically to avoid overfitting parameters to historical
performance, which would undermine the credibility of every downstream result.

**Disclosed consequence:** because Low Volatility and Maximum Drawdown carry meaningful
correlation, the nominal 20/20/20 split across the three risk-control factors somewhat overstates
how many independent risk dimensions are actually being controlled for. This is a known
characteristic of the current design, not corrected for.

## 3. Portfolio Construction (NB03)

**No additional liquidity filter beyond the factor panel's own history requirement.** A stock
needs all four factor z-scores present, which itself requires ~756 trading days of history for
Maximum Drawdown/Beta. The point-in-time coverage question flagged in NB01/NB02 is resolved as
"the history requirement alone is sufficient" — an explicit choice, not an implicit gap.

**Why Equal Weight.** Serves as the naive baseline against which any more sophisticated weighting
scheme should be judged.

**Why ERC (True Equal Risk Contribution), not inverse-volatility weighting.** ERC solves for
weights where each stock contributes *equally to total portfolio risk*, accounting for
correlation structure via the full covariance matrix — a materially different (and more
correct) risk-diversification objective than simple inverse-volatility weighting, which ignores
covariance entirely.

**ERC covariance lookback: 756 trading days**, matching the Maximum Drawdown/Beta window
elsewhere in the project, for consistency across the factor and risk model.

**Under-sized eligible universe (<20 stocks) at a rebalance date:** construct from all eligible
stocks available, log the event explicitly, never relax or tighten the filtering rule to
compensate. (This never triggered in the actual backtest — see `key_findings.md`.)

**Sector/industry fetch moved forward into NB03** (originally planned for NB05 in the project
blueprint) so Portfolio Research outputs (sector allocation, buffer-zone analysis) are available
immediately after construction, and so NB05 can reuse the cached map without re-fetching.

## 4. Walk-Forward Backtest (NB04)

**Transaction cost formula: one-way turnover × `DEFAULT_COST_BPS` (buy and sell combined, not
doubled).** An explicit choice, disclosed so it is not mistaken for the only possible reading of
"20bps per side."

**Weight drift between rebalances, not daily re-weighting.** A real portfolio is not re-weighted
every day; weights are held at target and allowed to drift with daily returns until the next
rebalance date.

**No transaction cost at the first rebalance** — no prior portfolio exists to unwind, and no
return is realised on that date (nothing to compound yet).

**Missing daily returns filled at 0%, logged as a diagnostic** — never silently absorbed into the
return series without a record.

**No benchmark overlay in this notebook.** Strictly portfolio-only; NIFTY 50 and Universe Average
Return comparisons are entirely NB05's role, keeping this notebook's scope to mechanics only.

## 5. Portfolio Analytics (NB05)

**Universe Average Return computed over the full AMFI-eligible universe**, not restricted to the
ranking-eligible subset (stocks with a valid composite score that day). A broader, and arguably
fairer, "did we beat just owning the universe" benchmark.

**Cost-sensitivity sweep implemented as closed-form**, not by re-running the full engine at every
cost level — exact because turnover does not depend on the cost assumption, only the resulting
deduction does. Validated by re-running the full engine at one alternate level and confirming
agreement to floating-point precision, rather than trusting the closed-form on faith.

**Metric table ordering: portfolio-level first, benchmark-relative second.** Describes the
strategy on its own terms before describing it relative to the market — the ordering itself is a
disclosed editorial choice about what a reviewer should read first.

**Rotation Analysis and Factor Attribution are purely descriptive.** Neither touches portfolio
construction or backtest logic; both read already-produced NB02/NB03/NB04 outputs and report what
the existing selections already look like. Both are explicitly disclosed as project extensions
beyond the original blueprint, confirmed with the project owner before implementation.

**Rotation Analysis restricted to the baseline combination (Quarterly + Equal Weight).** Six full
combinations would be six times the analytical work for a diagnostic that is illustrative rather
than decision-relevant to the core backtest result.

## 6. Regime Diagnostics (NB06)

**3-state Gaussian HMM (Bull / Bear / High Volatility), not 2-state.** Matches the project's
locked scope — three states specifically to separate ordinary bull/bear conditions from a
distinct high-volatility regime, rather than treating volatility as a byproduct of the other two.

**Fit on NIFTY 50 returns only, never on portfolio returns, never feeding back into portfolio
construction.** This is a strictly observational diagnostic layered on top of an already-complete
backtest, not a regime-switching strategy input.

**Fit over the full available NIFTY history, but the performance overlay restricted to the
backtest window.** More history helps the HMM separate regimes more robustly; the overlay is
restricted to where portfolio returns actually exist.

**State labels assigned post-hoc from fitted per-state statistics, not assumed by state index.**
`hmmlearn` does not guarantee any particular ordering of fitted states. Volatility is checked
first (High Volatility = highest mean volatility of the three) because "High Volatility" is this
project's explicit third label, not an in-between Bull/Bear state — disclosed as the deliberate
tie-breaking priority rather than left implicit.

**Regime persistence explicitly reported** (transition matrix, expected duration, short-run
count) because 3-state HMMs are known to sometimes produce unstable or short-lived states — a
limitation of the modelling choice, disclosed rather than hidden.

**Overlay restricted to the baseline combination**, consistent with NB05's Rotation Analysis and
sector-weight-over-time precedent, for the same reason: an illustrative diagnostic does not
warrant six times the analytical surface area.

## 7. Where This Project Deviates From the Original Specification

1. Factor set: original Momentum/Quality/Value/Low-Vol (25% each) → locked
   Momentum/Low-Vol/Max-Drawdown/Beta at 40/20/20/20.
2. Secondary benchmark: originally "NIFTY LargeMidcap 250" → actual Universe Average Return.
3. Universe reconstitution: unspecified originally → fully dynamic in the built version.
4. Sector/Industry fetch: originally planned for NB05 → brought forward into NB03.
5. Transaction cost formula: originally "20bps, per side" without a precise formula →
   operationalised as one-way-turnover × cost_bps.
6. Cost-sensitivity sweep: not specified originally → implemented as closed-form, independently
   validated.
7. Rotation Analysis, Factor Attribution, and the full Regime Diagnostics notebook fulfil the
   original specification's "Future Extensions" intent, each confirmed with the project owner
   before implementation.

Every deviation above was a disclosed, confirmed decision — not an undocumented drift from the
original scope.
