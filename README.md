# Quant Research Portfolio — Vidyasagar

A curated portfolio of quantitative finance projects on  
time-series analysis, stochastic modelling, Monte Carlo simulation, volatility modelling,  
and cross-asset correlation research.  

These projects reflect real quant workflows and are inspired by methods used at  
top hedge funds, trading firms, and investment banks.

---

## Projects Overview

All projects are located inside the `notebooks/` folder, each with its own README  
and example outputs.

### **1. Monte Carlo Option Pricing (GBM)**
Folder: `notebooks/MonteCarlo_GBM/`  
- Simulates stock price paths under **Geometric Brownian Motion**  
- Prices a European call option using Monte Carlo  
- Shows convergence as simulation paths increase  
- Includes distribution of terminal stock price \( S_T \)

---

### **2. Volatility Modelling Using GARCH(1,1)**
Folder: `notebooks/GARCH_RealData/`  
- Fits a **GARCH(1,1)** model on real market data  
- Compares sample volatility vs GARCH conditional volatility  
- Plots realized vs model-based volatility  
- Uses both to price a European option (`Monte Carlo + Black–Scholes`)

---

### **3. Lead–Lag Cross-Correlation Analysis**
Folder: `notebooks/CrossCorrelation/`  
- Evaluates correlation between two assets across time lags  
- Includes **permutation-based significance testing**  
- Inspired by ZDCF techniques used in astrophysics  
- Produces rolling correlation, lag correlation stem plot, and price/returns charts  

## Quickstart
1. Create a Python environment and install dependencies:
```bash
pip install -r requirements.txt

## 2) `requirements.txt`

> Note: exact versions are flexible — these will cover the code in your notebooks. Add extras later as needed.

## 3) `.gitignore`


```markdown
## How to run
1. Install dependencies: `pip install -r requirements.txt`  
2. Open the notebook in Colab or Jupyter and run cells top-to-bottom.

## Checklist
- [ ] Notebook runs top-to-bottom without errors (Colab recommended)
- [ ] requirements.txt updated if new libraries were used
- [ ] No large raw datasets committed (data/ contains only tiny sample)
- [ ] `notebooks/<Project>/results/` contains example PNG outputs (small)
