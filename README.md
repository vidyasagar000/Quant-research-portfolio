# Quant Research Portfolio — Vidyasagar

This repository contains quantitative finance projects demonstrating time-series analysis, volatility modelling, Monte Carlo option pricing, and cross-asset correlation research. Each project is organized inside `notebooks/` with a short README and example outputs.

## Projects (folders)
- `notebooks/MonteCarlo_GBM/` — Monte Carlo simulation for European option pricing under GBM.  
- `notebooks/GARCH_RealData/` — GARCH(1,1) volatility modeling on real stock data and its effect on option valuation.  
- `notebooks/CrossCorrelation/` — Lead–lag cross-correlation analysis inspired by ZDCF, with permutation-based significance testing.

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
