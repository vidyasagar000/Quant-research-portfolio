# Quant Research Portfolio — Vidyasagar Bhat

A portfolio of quantitative finance research projects built on a foundation in physics and astrophysics research. Each project applies rigorous statistical and computational methods — stochastic modelling, time-series analysis, factor models, and probabilistic inference — to real market data.

Projects are progressive, not isolated. Each one addresses a question the previous one raised.

---

## The Research Arc

```
How do asset prices evolve stochastically?
        ↓
Monte Carlo Option Pricing — simulate GBM paths, price European options

How does volatility actually behave over time?
        ↓
GARCH Volatility Modelling — model time-varying volatility, compare to implied vol

Do assets move independently or together?
        ↓
Lead-Lag Cross-Correlation — identify directional relationships, test significance

Does market behaviour stay constant or shift across regimes?
        ↓
Regime Detection — K-Means clustering + Hidden Markov Model

Where does portfolio risk actually come from?
        ↓
Risk Decomposition — CAPM, Portfolio Attribution, Fama-French across US and India

Can a systematic strategy actually harvest return while surviving real-world frictions?
        ↓
Multi-Factor Portfolio Framework — factor construction, walk-forward backtest,
rotation & regime diagnostics on Indian equities
```

---

## Projects

### 1. Monte Carlo Option Pricing
`notebooks/MonteCarlo_GBM/`

Simulates stock price paths under Geometric Brownian Motion and prices European call options using Monte Carlo integration. Demonstrates convergence behaviour as simulation paths increase and shows the distribution of terminal stock prices.

**Key concept:** Risk-neutral pricing, GBM path simulation, convergence analysis.

---

### 2. GARCH Volatility Modelling
`notebooks/GARCH_RealData/`

Fits a GARCH(1,1) model on real market data and compares GARCH conditional volatility against implied volatility from options markets. Uses both as inputs to Black-Scholes pricing and documents the gap between the two estimates.

**Key finding:** GARCH and implied volatility diverge systematically — implied vol contains a risk premium that backward-looking statistical models cannot capture.

---

### 3. Lead-Lag Cross-Correlation Analysis
`notebooks/CrossCorrelation/`

Evaluates time-lagged correlations between two assets using permutation-based significance testing. Identifies whether price leadership exists between assets — whether one asset's movement today predicts another's movement tomorrow.

**Key concept:** Cross-correlation structure, statistical significance, rolling correlation dynamics.

---

### 4. Market Regime Detection
`notebooks/Regime_Detection/`

Applies two complementary approaches to identify latent market states across US and Indian assets — K-Means clustering as a static baseline and a Gaussian Hidden Markov Model as a sequential probabilistic model. Applied to META, TSLA, Nifty 50, and HDFC Bank.

**Key finding:** K-Means classified 77% of META's history as Bull. HMM classified only 31% as Bull — because HMM understands that a moderate-return day inside a sustained volatile period is not a bull day. The models agree on what regimes exist but disagree on when they occur, and HMM's time-ordering makes it the more defensible choice for production risk systems.

---

### 5. Risk Decomposition — US & India
`notebooks/Risk_Decomposition/`

Applies CAPM beta decomposition, portfolio risk attribution, and Fama-French 3-factor analysis to equal-weighted portfolios in two markets. The same framework runs on both a US portfolio (S&P 500 benchmark) and an Indian portfolio (Nifty 50 benchmark) to test whether factor models reveal different risk structures across developed and emerging markets.

**Key finding:** Indian stocks have 62.9% average idiosyncratic risk vs 54.9% for US stocks — the Nifty 50 is a weaker systematic factor than the S&P 500. Equal weight produces highly unequal risk in both markets. Adding Fama-French factors reclassified large portions of apparent idiosyncratic risk as value factor exposure — increasing JPM's explained variance by 22% and XOM's by 23%.

---

### 6. Multi-Factor Portfolio Research Framework — Indian Equities
`notebooks/Multi-Factor Portfolio Research/`

Risk Decomposition establishes *where* risk comes from and shows that equal weight is not equal risk. This project asks the natural next question: can that understanding be turned into an actual systematic strategy, built and stress-tested the way an institutional desk would? A six-notebook pipeline constructs a dynamically-reconstituted Indian Large + Mid Cap universe, ranks it on a four-factor composite (Momentum, Low Volatility, Maximum Drawdown, Beta), selects a Top-20 portfolio under two weighting schemes (Equal Weight vs. True Equal Risk Contribution) and three rebalancing frequencies, then runs all six combinations through a realistic walk-forward backtest — weight drift, transaction costs, the full mechanics — before evaluating the result on benchmark-relative performance, holdings-rotation quality, factor attribution, and market-regime conditioning. The regime layer reuses the same Gaussian HMM approach from Project 4, now applied to a live backtest rather than a standalone classification exercise.

**Key finding:** every one of the six (frequency × weighting) combinations outperformed both NIFTY 50 and a Universe Average Return benchmark on every risk-adjusted metric, and that edge held in every HMM-identified regime — Bull, Bear, and High Volatility alike, not just on average. But Rotation Analysis complicates the headline: only 48.4% of individual holdings swaps were profitable in hindsight, and the top 3 rotations accounted for 42% of all positive replacement alpha. The outperformance is real, but it is concentrated in a handful of large successful trades rather than a uniformly consistent signal — the same instinct as Project 4's regime work: two ways of looking at the same result can each be correct and still tell different stories, and the less flattering one is usually the more honest one.

---

## Key Themes Across All Projects

**Markets are non-stationary.** GARCH shows volatility clusters. Regime detection shows behaviour shifts. Risk decomposition shows correlations change in crises. The Multi-Factor Portfolio's regime overlay confirms it a fourth way — and confirms outperformance survives it.

**Equal weight is not risk management.** US and Indian portfolios both show that identical position weights produce dramatically unequal risk contributions; the Multi-Factor Portfolio project tests the natural fix (Equal Risk Contribution) directly against it and finds the difference is real but modest.

**Model choice matters.** K-Means vs HMM. CAPM vs Fama-French. GARCH vol vs implied vol. Mean vs median replacement alpha. Each pair tells a different story from the same data — and understanding why they differ is where the real insight lives.

**A headline number is not the whole answer.** Outperformance, on its own, is the least interesting finding in the Multi-Factor Portfolio project — what matters is that it survives a transaction-cost sweep, holds across every market regime, and is honestly disclosed as concentrated in a handful of trades rather than uniformly consistent. Every project in this portfolio ends with the equivalent question: what does this result look like once you stress-test it?

**Physics instincts transfer.** Separating signal from noise. Distinguishing systematic from idiosyncratic. Testing model assumptions before trusting outputs. These are physics research habits applied to financial data, all the way through to a full portfolio backtest.

---

## Setup

```bash
pip install -r requirements.txt
```

Each notebook is self-contained. Run cells top to bottom. All data downloaded fresh via `yfinance`, `pandas-datareader`, and (for the Multi-Factor Portfolio project) AMFI's public stock categorisation data.

---

## Tech Stack

Python · NumPy · Pandas · Matplotlib · Statsmodels · Scikit-learn · SciPy · ARCH · hmmlearn · yfinance

---

## Structure

```
Quant-research-portfolio/
│
├── notebooks/
│   ├── MonteCarlo_GBM/
│   ├── GARCH_RealData/
│   ├── CrossCorrelation/
│   ├── Regime_Detection/
│   ├── Risk_Decomposition/
│   │   ├── US_Portfolio/
│   │   └── India_Portfolio/
│   └── Multi-Factor Portfolio Research/
│       ├── NB01_Universe_Construction.ipynb
│       ├── NB02_Factor_Research.ipynb
│       ├── NB03_Portfolio_Construction.ipynb
│       ├── NB04_WalkForward_Backtest.ipynb
│       ├── NB05_Portfolio_Analytics.ipynb
│       ├── NB06_Regime_Diagnostics.ipynb
│       └── docs/                  # methodology, design decisions, key findings
│
├── requirements.txt
└── README.md
```
