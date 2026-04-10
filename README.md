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

## Key Themes Across All Projects

**Markets are non-stationary.** GARCH shows volatility clusters. Regime detection shows behaviour shifts. Risk decomposition shows correlations change in crises. Every project confirms this from a different angle.

**Equal weight is not risk management.** Both US and Indian portfolios show that identical position weights produce dramatically unequal risk contributions.

**Model choice matters.** K-Means vs HMM. CAPM vs Fama-French. GARCH vol vs implied vol. Each pair tells a different story from the same data — and understanding why they differ is where the real insight lives.

**Physics instincts transfer.** Separating signal from noise. Distinguishing systematic from idiosyncratic. Testing model assumptions before trusting outputs. These are physics research habits applied to financial data.

---

## Setup

```bash
pip install -r requirements.txt
```

Each notebook is self-contained. Run cells top to bottom. All data downloaded fresh via `yfinance` and `pandas-datareader`.

---

## Tech Stack

Python · NumPy · Pandas · Matplotlib · Statsmodels · Scikit-learn · ARCH · hmmlearn · yfinance

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
│   └── Risk_Decomposition/
│       ├── US_Portfolio/
│       └── India_Portfolio/
│
├── requirements.txt
└── README.md
```
