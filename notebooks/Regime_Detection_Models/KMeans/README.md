# K-Means Regime Detection — Meta Platforms

> Detecting latent market regimes in equity time series using K-Means Clustering applied to a multi-dimensional feature set built from 10 years of daily price data.

---

## What is Regime Detection?

Financial markets are non-stationary — the statistical properties of returns and volatility shift over time. A model calibrated during a calm bull market will systematically fail during a crisis. Regime detection identifies **when the market has fundamentally changed its behaviour** and characterises what each distinct state looks like.

---

## Model — K-Means Clustering

K-Means groups each trading day into one of *k* clusters by minimising within-cluster variance across the feature space. Each day is classified **independently** — the model has no memory of what came before.

**Strengths**
- Fast, interpretable, no distributional assumptions
- Works well for static regime profiling

**Core limitation**
- Treats every day as independent — ignores the sequential, persistent nature of market regimes
- Transition matrix is computed post-hoc, not learned by the model

---

## Features

| Feature | Formula | What it captures |
|---|---|---|
| `Return` | ln(Pₜ / Pₜ₋₁) | Daily direction and magnitude |
| `GARCH_vol` | GARCH(1,1) conditional σ | Forward-looking volatility estimate |
| `Volatility` | 20-day rolling σ | Realised short-term volatility |
| `Momentum` | Pₜ / Pₜ₋₂₀ − 1 | 1-month trend — scale-independent |
| `Drawdown` | (Pₜ − max P) / max P | Distance from all-time high |

---

## Primary Asset — Meta Platforms (META)

10 years of daily data · May 2016 → April 2026 · 2,493 trading days

### Price History

![Price History](plots/01_price_history.png)

---

### Volatility Clustering — Motivation for Regime Detection

High-volatility periods cluster together rather than being randomly dispersed. This is the empirical justification for regime modelling — if volatility were i.i.d., there would be no persistent states to detect.

![Volatility Clustering](plots/02_volatility_clustering.png)

---

### Model Selection — Elbow & Silhouette

k = 2 peaks on silhouette score (mathematically cleanest). **k = 3 is chosen** for financial interpretability — Bull / Correction / Crash maps to actionable, distinct market states. Silhouette at k = 3 remains acceptable at 0.47.

![Model Selection](plots/03_elbow_silhouette.png)

---

### Regime-Colored Price Chart

Each dot represents one trading day, colored by detected regime. The 2022–2023 collapse and COVID flash crash are correctly identified as Crash/Correction episodes.

![Regime Chart](plots/04_regime_chart.png)

---

### Regime Distribution

META spends 77.2% of its history in a Bull regime — consistent with its strong long-term growth trajectory. Crash episodes account for only 3.5% of days but produce the most severe drawdowns.

![Regime Distribution](plots/05_regime_distribution.png)

---

### Return Distributions Per Regime

The x-axis width tells the story — Bull returns are tight (σ = 1.83%), Correction is wider (σ = 3.37%), and Crash is extreme (σ = 5.83%) with visible fat tails extending to ±30%. This fat-tail structure is why parametric Gaussian VaR underestimates crash-regime risk.

![Return Distributions](plots/06_return_distributions.png)

---

### Transition Matrix

Computed empirically after classification — **not a model parameter**. Diagonal values show regime persistence. Notable: Crash → Bull direct transition is 0.000 — recoveries always pass through Correction first.

![Transition Matrix](plots/07_transition_matrix.png)

---

### Price vs Rolling VaR (99%)

VaR spikes during the 2018 Q4 selloff, COVID crash, and the 2022 META collapse are clearly visible. Spikes in the red band coincide with the Crash regime classifications above.

![VaR Overlay](plots/08_var_overlay.png)

---

## Key Results — Meta Platforms

| Regime | Return | Volatility | Drawdown | VaR 99% | Avg Duration |
|---|---|---|---|---|---|
| **Bull** | +0.131% | 1.70% | −8.26% | −4.41% | 66.4 days |
| **Correction** | +0.038% | 3.19% | −41.28% | −7.86% | 12.3 days |
| **Crash** | −1.206% | 6.09% | −44.30% | −12.37% | 6.8 days |

### Regime Persistence

| Regime | Daily Persistence | Expected Duration |
|---|---|---|
| Bull | 98.5% | ~66 days |
| Correction | 92.1% | ~13 days |
| Crash | 85.2% | ~7 days |

**Crash → Bull direct transition: 0.0%** — the market never jumped directly from crash to bull in 10 years of META data. Recovery always passes through a correction phase first.

---

## Cross-Asset Overview

The same model applied to Tesla, NIFTY 50, and HDFC Bank. Each asset exhibits distinct regime dynamics — Tesla shows rapid regime switching post-2020 while NIFTY 50 shows longer, more persistent bull phases.

![Cross Asset](plots/09_cross_asset_regimes.png)

---

## Limitations

- **No time ordering** — every day classified in isolation; ignores regime persistence
- **Transition matrix is post-hoc** — not a model parameter, computed after classification
- **Parametric Gaussian VaR** — underestimates tail risk in crash regimes due to fat-tailed return distributions
- **In-sample only** — no out-of-sample or walk-forward validation
- **n = 3 chosen over statistical optimum** — silhouette peaks at k = 2; k = 3 chosen for financial interpretability

---

## How to Run

```bash
pip install yfinance pandas numpy matplotlib seaborn scikit-learn arch
jupyter notebook KMeans_Regime_Detection.ipynb
```

Change `SYMBOL` at the top of the notebook to run on any asset:

```python
SYMBOL = 'META'       # Options: 'META', 'TSLA', '^NSEI', 'HDFCBANK.NS'
```

---

*See the `HMM/` folder for the time-aware probabilistic model that addresses the core limitation of K-Means — the lack of sequential memory.*
