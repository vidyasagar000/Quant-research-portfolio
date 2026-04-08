# Risk Decomposition — A Cross-Market Factor Analysis

A quantitative research project applying systematic risk decomposition models to equity portfolios in two markets — the US (S&P 500) and India (Nifty 50). The central research question is whether the same factor models reveal different risk structures across a developed and an emerging market.

Built by a physicist transitioning into quantitative finance — applying rigorous statistical modelling, stochastic thinking, and empirical analysis to understand what actually drives portfolio risk.

---

## Research Question

> *Does the same risk decomposition framework reveal different structures in US vs Indian equity markets? How much of individual stock risk is truly systematic, and how much is genuinely idiosyncratic — and does this differ between markets?*

---

## Project Structure

```
risk-decomposition/
│
├── US_Portfolio/
│   ├── 01_capm_dark.ipynb              ← CAPM beta decomposition
│   ├── 02_portfolio_risk_dark.ipynb    ← Portfolio risk attribution
│   ├── 03_fama_french_dark.ipynb       ← Fama-French 3-factor model
│   ├── output/                         ← All saved charts
│   └── README.md
│
└── India_Portfolio/
    ├── india_01_capm_dark.ipynb        ← CAPM beta decomposition
    ├── india_02_portfolio_risk_dark.ipynb  ← Portfolio risk attribution
    ├── output/                         ← All saved charts
    └── README.md
```

---

## Portfolios

**US Portfolio — 5 stocks, equal weighted, S&P 500 benchmark**

| Stock | Ticker | Sector |
|---|---|---|
| Apple | AAPL | Technology |
| JPMorgan Chase | JPM | Financials |
| ExxonMobil | XOM | Energy |
| Johnson & Johnson | JNJ | Healthcare |
| Amazon | AMZN | Consumer / Technology |

**India Portfolio — 5 stocks, equal weighted, Nifty 50 benchmark**

| Stock | Ticker | Sector |
|---|---|---|
| Reliance Industries | RELIANCE.NS | Energy / Conglomerate |
| Tata Consultancy Services | TCS.NS | IT Services |
| HDFC Bank | HDFCBANK.NS | Banking |
| Infosys | INFY.NS | IT Services |
| ITC | ITC.NS | FMCG |

**Sample Period:** January 2018 — January 2024  
*Captures multiple regimes: COVID crash (2020), post-pandemic bull market (2021), Fed rate hike cycle (2022), and subsequent recovery.*

---

## Models Applied

### CAPM — Single Factor Model
$$R_i = \alpha_i + \beta_i \cdot R_m + \epsilon_i$$

Decomposes each stock's total variance into systematic (market-driven) and idiosyncratic (stock-specific) components. Applied to both portfolios.

### Portfolio Risk Attribution
$$\sigma_p^2 = w^T \Sigma w \quad \text{with} \quad CCR_i = w_i \cdot \frac{(\Sigma w)_i}{\sigma_p}$$

Attributes total portfolio volatility back to each position using Marginal and Component Contribution to Risk. Applied to both portfolios.

### Fama-French 3-Factor Model
$$R_i - R_f = \alpha_i + \beta_1(R_m - R_f) + \beta_2 \cdot SMB + \beta_3 \cdot HML + \epsilon_i$$

Extends CAPM with size (SMB) and value (HML) factors. Applied to US portfolio only — official Fama-French factor data is not available for Indian markets through Kenneth French's data library. This constraint is acknowledged explicitly rather than worked around.

---

## Key Findings

### Finding 1 — Equal weight never means equal risk

In both markets, identical 20% position weights produce highly unequal risk contributions. This is true in the US (AAPL 23.5% vs XOM 11.1%) and in India (INFY 23.1% vs HDFCBANK 16.2%). Weight-based thinking is not risk management.

### Finding 2 — Individual volatility is a poor predictor of portfolio risk contribution

The most striking result across both portfolios. In the US, XOM is the most volatile individual stock (32.56%) but the best portfolio diversifier (11.1% risk contribution). In India, HDFCBANK has the highest market sensitivity (R²=0.58) but the lowest risk contribution (16.2%). What drives portfolio risk is not individual volatility but correlation structure — how a stock moves relative to everything else simultaneously.

### Finding 3 — Indian markets have significantly higher idiosyncratic risk

| Metric | US | India |
|---|---|---|
| Avg R² vs Index | 0.451 | 0.371 |
| Avg Idiosyncratic % | 54.9% | 62.9% |

The Nifty 50 explains less of individual Indian stock movements than the S&P 500 does for US stocks. Indian equities are driven by FII flows, currency dynamics, global commodity prices, and domestic regulatory events — forces that the domestic index does not capture. Stock selection matters more in India as a result.

### Finding 4 — CAPM systematically underestimates systematic risk

For JPM and XOM, adding just two Fama-French factors (SMB and HML) increased R² by 22% and 23% respectively. Risk that appeared idiosyncratic under CAPM was actually exposure to the value premium — a known, systematic, replicable factor. This has direct implications for hedging: XOM's risk cannot be effectively hedged with S&P 500 instruments because 55% of its variance is now understood to come from the value factor, not the market factor.

### Finding 5 — The portfolio contains a structural value vs growth divide

JPM (HML=+0.86) and XOM (HML=+0.95) are deep value stocks. AAPL (HML=-0.39) and AMZN (HML=-0.74) are growth stocks. These groups perform differently across macro regimes — value outperforms during rate hike cycles and inflationary periods (2022), growth outperforms during low-rate expansions (2019-2021). The portfolio's factor exposure partially explains its behaviour across different economic environments.

### Finding 6 — Diversification benefit is similar across markets

Despite very different risk structures, both portfolios delivered comparable absolute diversification benefits — 8.44% (US) vs 8.57% (India). A well-constructed cross-sector portfolio delivers similar diversification value regardless of market development level. What differs is the source of that benefit: in the US it comes from sector independence across tech, energy, healthcare, and financials; in India it comes from the genuine separation between domestic banking, global IT exports, and commodity-linked conglomerates.

### Finding 7 — Global crises hit both markets simultaneously

The COVID crash in March 2020 drove rolling 60-day portfolio volatility to approximately 60% (US) and 55% (India) — nearly identical peaks. Domestic risk structure differences disappear in global crisis events. The correlation between markets that appear independent in normal regimes rises sharply during stress — a well-documented phenomenon that both portfolios confirm empirically.

---

## Model Progression — Why Three Notebooks

The three US notebooks are intentionally sequential, not parallel:

**CAPM** identifies what is systematic vs idiosyncratic using one factor. The limitation: JPM (R²=0.55) and XOM (R²=0.32) have large unexplained portions.

**Portfolio Risk Attribution** shows that the combination of stocks matters as much as their individual characteristics. XOM's low correlations make it a better diversifier than its individual volatility suggests.

**Fama-French** addresses CAPM's limitation directly. By adding size and value factors, it reclassifies much of the unexplained variance as factor exposure — increasing JPM's R² to 0.77 and XOM's to 0.55. JNJ remains stubbornly at R²=0.35 even with three factors, confirming that healthcare-specific dynamics are genuinely idiosyncratic and beyond the reach of standard factor models.

This progression mirrors the actual development of asset pricing theory over three decades.

---

## Setup

```bash
pip install -r requirements.txt
```

Each notebook is self-contained — run cells top to bottom. Data is downloaded fresh via `yfinance`. Fama-French factors are downloaded from Kenneth French's data library in Notebook 03.

---

## Tech Stack

Python · NumPy · Pandas · Matplotlib · Statsmodels · yfinance · Scikit-learn
