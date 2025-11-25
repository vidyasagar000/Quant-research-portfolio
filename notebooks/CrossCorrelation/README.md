# Cross-Correlation Analysis of BTC and ETH

Price dynamics and daily log-returns

<img width="1089" height="590" alt="Prices vs Daily log-returns" src="https://github.com/user-attachments/assets/a589b7d5-4d3e-4875-ad18-c6b541aa03d5" />

This figure shows the price evolution of Bitcoin (BTC-USD) and Ethereum (ETH-USD) over the last five years, along with their corresponding daily log-returns. BTC and ETH exhibit strong co-movement in their price trajectories, reflecting their shared sensitivity to macro events, liquidity cycles, and overall crypto-market risk sentiment. The return series show high volatility, sharp jumps, and heavy-tailed behavior — typical characteristics of cryptocurrency markets. Plotting both prices and log-returns makes it easy to visually appreciate how strongly the two assets move together.

Rolling correlation (60-day window)

<img width="989" height="390" alt="60 - day rolling correlation" src="https://github.com/user-attachments/assets/2763a188-ebd9-40d3-9055-89f38bdf04f3" />

The 60-day rolling correlation highlights the time-varying relationship between BTC and ETH returns. Throughout most of the five-year period, the rolling correlation remains consistently high (typically between 0.70 and 0.95), showing that the two assets behave as part of the same risk cluster. However, temporary declines in correlation occur during periods of market stress or asset-specific news. This rolling view demonstrates that correlations in financial markets are not static — they evolve as market regimes change — a crucial insight for portfolio construction, diversification, and risk management.

Lagged cross-correlation and statistical significance

<img width="826" height="374" alt="CrossCorrelation vs Lag" src="https://github.com/user-attachments/assets/ab20d84a-a8c0-4039-ad97-5eac940ab810" />

The cross-correlation-vs-lag plot quantifies whether BTC leads ETH, or vice-versa, at different time lags. The strongest peak occurs at lag = 0, with a correlation of 0.8131, meaning both assets move simultaneously with no detectable lead–lag effect. Nearby lags (±2, ±4 days) also show similarly high correlations, reinforcing that BTC and ETH respond almost instantly to market-wide information.
The permutation test gives a p-value ≈ 0.002, confirming that this strong correlation is highly statistically significant and extremely unlikely to occur by chance. This validates the robustness of the BTC–ETH relationship and demonstrates the effectiveness of cross-correlation as a tool for understanding directional influence between financial assets.
