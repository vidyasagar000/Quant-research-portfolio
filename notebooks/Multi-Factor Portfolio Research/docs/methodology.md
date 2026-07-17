# Methodology

Full computational methodology for the Multi-Factor Portfolio Research Framework, organised by
pipeline stage. Formulas here match what is actually implemented in the notebooks; see the
notebooks themselves for code.

---

## 1. Universe Construction (NB01)

**Universe definition:** SEBI Large Cap + Mid Cap, per AMFI's semi-annual stock categorisation.
Reconstituted **dynamically** — 17 snapshots, 2018-06-30 → latest — rather than fixed at a single
point in time, deferring point-in-time eligibility to each rebalance date rather than applying
a single global filter.

**Study window:**
- `WARMUP_START = 2015-07-01` (price history for factor lookback only)
- `BACKTEST_START = 2018-07-01` (first date with a genuine AMFI classification snapshot)
- `END_DATE` = dynamic (latest available)

**Corporate Action Registry:** every ticker receives one of four explicit dispositions —
`ticker_change` (re-downloaded under new symbol), `merger` (excluded, absorbed elsewhere),
`delisted` (excluded, no successor), `unknown` (flagged for manual review). No ticker is silently
dropped; every disposition is logged to `data/ticker_resolution_log.csv`.

**Returns:**

$$r_t = \frac{P_t}{P_{t-1}} - 1 \qquad r_t^{\log} = \ln\left(\frac{P_t}{P_{t-1}}\right)$$

Both simple and log returns are computed and persisted; compounding uses simple returns.

---

## 2. Factor Model (NB02, v2 — locked)

Four factors, computed entirely from price/return data at every month-end trading date from
`BACKTEST_START` onward:

| Factor | Formula | Lookback | Direction |
|---|---|---:|---|
| Momentum | $\dfrac{P_{t-\text{skip}}}{P_{t-\text{skip}-\text{lookback}}} - 1$, skip = 21d | 252d | Higher = better |
| Low Volatility | $\text{std}(r_{t-251},\dots,r_t) \times \sqrt{252}$ | 252d | Lower = better (inverted at z-score stage) |
| Maximum Drawdown | $\min_{t' \in [t-755, t]} \left(\dfrac{P_{t'}}{\max_{\tau \le t'} P_\tau} - 1\right)$ | 756d (~3yr) | Less negative = better |
| Beta | $\dfrac{\text{Cov}(r_{\text{stock}}, r_{\text{bench}})}{\text{Var}(r_{\text{bench}})}$, then scored as $-|\beta - 1.0|$ | 756d (~3yr) | Closest to 1.0 = best |

Momentum follows the Jegadeesh–Titman 12-1 specification (skip the most recent month to avoid
short-term reversal). All four factors are winsorised (z-scored, clipped at ±3.0) cross-sectionally
at each rebalance date; a stock is eligible for ranking only if all four z-scores are present that
date.

**Composite score:**

$$\text{Composite}(t, i) = 0.40 \cdot z_{\text{Mom}}(t,i) + 0.20 \cdot z_{\text{LowVol}}(t,i) + 0.20 \cdot z_{\text{MaxDD}}(t,i) + 0.20 \cdot z_{\text{Beta}}(t,i)$$

Weights are locked by design intent (Section 2, `design_decisions.md`), never tuned against
backtest performance.

---

## 3. Portfolio Construction (NB03)

**Selection:** Top 20 stocks by composite score at each rebalance date. If fewer than 20 are
eligible, all eligible stocks are taken and the shortfall logged (this never triggered in
practice — see `key_findings.md`).

**Equal Weight:**

$$w_i = \frac{1}{N}, \quad i = 1,\dots,N$$

**True Equal Risk Contribution (ERC):** solved via `scipy.optimize`, long-only, fully invested,
no leverage. Portfolio variance and per-stock risk contribution:

$$\sigma_p^2 = w^\top \Sigma w \qquad \text{RC}_i = w_i (\Sigma w)_i$$

ERC objective:

$$\min_w \sum_{i=1}^N \left(\text{RC}_i - \frac{\sigma_p^2}{N}\right)^2 \quad \text{s.t.} \quad \sum_i w_i = 1,\ w_i \ge 0$$

$\Sigma$ is estimated from the trailing 756-trading-day covariance of daily returns. Covariance
matrices with condition number above $10^6$ are shrunk 10% toward their diagonal before
optimisation; combinations with fewer than 100 observations fall back to Equal Weight (logged).

**Concentration diagnostic (Herfindahl-Hirschman Index):**

$$\text{HHI} = \sum_{i=1}^N w_i^2, \qquad \text{Effective } N = \frac{1}{\text{HHI}}$$

**Rebalancing frequencies:** Monthly / Quarterly / Semi-Annual, each a subset of NB02's monthly
grid (every 1st / 3rd / 6th month-end) rather than recomputed at coarser granularity.

---

## 4. Walk-Forward Backtest (NB04)

**Weight drift between rebalances** (no daily re-weighting, matching real portfolio behaviour):

$$w_i^{(t+1)} = \frac{w_i^{(t)}(1 + r_i^{(t)})}{\sum_j w_j^{(t)}(1 + r_j^{(t)})}$$

**Turnover and transaction cost at each rebalance:**

$$\text{turnover} = \frac{1}{2}\sum_i \left|w_i^{\text{target}} - w_i^{\text{drifted}}\right|, \qquad \text{cost} = \text{turnover} \times \text{DEFAULT\_COST\_BPS}$$

The buy and sell legs are treated as one combined cost (not doubled). Cost is deducted the same
day the rebalance-date return is realised, using the pre-rebalance drifted weights for that
return. No cost is charged at the first rebalance (no prior holdings to unwind). Missing daily
returns for a held stock are filled at 0% and logged as a diagnostic, never silently absorbed.

**Cumulative return** (no starting capital anywhere in this project):

$$\text{cumulative return}_T = \prod_{t=1}^{T}(1 + r_t)$$

---

## 5. Performance Metrics (NB05)

**Portfolio-level (risk-free rate $r_f = 0\%$ throughout):**

$$\text{CAGR} = \left(\prod_t(1+r_t)\right)^{252/n} - 1 \qquad \text{Ann. Vol} = \text{std}(r)\sqrt{252}$$

$$\text{Sharpe} = \frac{\text{mean}(r)\times 252 - r_f}{\text{Ann. Vol}} \qquad \text{Sortino} = \frac{\text{mean}(r)\times 252 - r_f}{\sqrt{\text{mean}(\min(r,0)^2)\times 252}}$$

$$\text{Max Drawdown} = \min_t\left(\frac{\text{cum}_t}{\max_{\tau\le t}\text{cum}_\tau} - 1\right) \qquad \text{Calmar} = \frac{\text{CAGR}}{|\text{Max Drawdown}|}$$

**Benchmark-relative (vs. NIFTY 50):**

$$\beta = \frac{\text{Cov}(r_p, r_b)}{\text{Var}(r_b)} \qquad \alpha_{\text{ann.}} = \left(\text{mean}(r_p) - \beta\cdot\text{mean}(r_b)\right)\times 252$$

$$\text{Tracking Error} = \text{std}(r_p - r_b)\sqrt{252} \qquad \text{Information Ratio} = \frac{\text{mean}(r_p - r_b)\times 252}{\text{Tracking Error}}$$

$$\text{Up Capture} = \frac{\text{mean}(r_p \mid r_b>0)}{\text{mean}(r_b \mid r_b>0)}\times 100 \qquad \text{Down Capture} = \frac{\text{mean}(r_p \mid r_b<0)}{\text{mean}(r_b \mid r_b<0)}\times 100$$

Portfolio-level Beta here is the **realised regression beta** of constructed-portfolio returns
against NIFTY 50 — distinct from the Beta *factor* used in stock selection (NB02), which scores
individual-stock closeness to 1.0 before any portfolio exists.

**Degenerate-input handling:** any zero-denominator case (zero volatility, zero benchmark
variance), checked within a $10^{-10}$ tolerance rather than exact `==0`, returns `NaN` rather
than `inf` or a crash.

**Universe Average Return (secondary benchmark):** equal-weighted average daily return across
every AMFI-eligible ticker each day (not restricted to the ranking-eligible subset), computed
directly from the constructed universe. A ticker missing a price on a given day is excluded from
that day's average only.

**Transaction-cost sensitivity sweep (0/10/20/50 bps), closed-form:**

$$\text{net\_return\_at\_bps} = \text{gross\_return} - \frac{\text{turnover\_pct}}{100}\times\frac{\text{bps}}{10000}\quad\text{(rebalance days only)}$$

Exact because turnover itself does not depend on the cost assumption — validated against a full
engine re-run at an alternate cost level (agreement to floating-point precision).

**Rotation Analysis — Replacement Alpha** (baseline combination: Quarterly + Equal Weight):

$$\text{Replacement Alpha} = \bar{r}^{\text{fwd}}_{\text{added}} - \bar{r}^{\text{fwd}}_{\text{removed}}$$

Equal-weighted forward compounded return of added tickers minus removed tickers, measured over
the forward holding window until the next rebalance (or end of available history for the final
one). A rotation event is a Success if Replacement Alpha is positive.

**Factor Attribution:** for each baseline rebalance date, mean factor z-score of the selected
Top-20 portfolio minus the mean factor z-score of the full eligible universe, averaged equally
across dates (not pooled across stock-days). Purely descriptive.

---

## 6. Regime Diagnostics (NB06)

**Features** (NIFTY 50 returns only, standardised before fitting):

$$z_j = \frac{x_j - \bar{x}_j}{\sigma_j}, \quad j \in \{\text{return}, \text{volatility}_{21d}, \text{drawdown}\}$$

**Model:** 3-state Gaussian HMM, `covariance_type='diag'`, fixed random seed, fit over the full
available NIFTY 50 history (not just the backtest window) for a more robust regime estimate.

**State labeling** (post-hoc, from fitted per-state statistics, since `hmmlearn` does not
guarantee state ordering):
1. **High Volatility** = state with the highest mean volatility of the three.
2. Of the remaining two: **Bull** = higher mean return, **Bear** = lower mean return.

**Regime persistence:** transition matrix, expected regime duration ($1/(1-p_{\text{self}})$ in
trading days), and count of runs lasting ≤3 days (flagged as potentially unstable).

**Regime-conditional performance overlay:** restricted to the backtest window and to the
baseline combination only (Quarterly + Equal Weight) — observational, never fed back into
portfolio construction.

---

## 7. Annualisation and Global Conventions

- 252 trading days/year for all annualisation.
- Risk-free rate fixed at 0% throughout.
- No starting capital or "portfolio value" language anywhere — every return series is a
  compounded-return series, first value 1.0 (a mathematical normalisation only).
- Dark-theme visual configuration and a shared `render_dark_table` table renderer are used
  identically across all six notebooks.

For the rationale behind each of these choices (why 756 days for Beta/Max Drawdown, why ERC
alongside Equal Weight, why these specific factors), see `design_decisions.md`.
