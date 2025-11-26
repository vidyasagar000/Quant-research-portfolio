# GARCH(1,1) — Volatility Modeling & Option Pricing (AAPL)

Realized Volatility (20-day rolling, annualized)
<img width="989" height="390" alt="Realized Volatility (20-day rolling, annualized)" src="https://github.com/user-attachments/assets/4d794c3b-4986-4a45-a954-7ff5caefc80b" />
The realized volatility plot shows the empirical 20-day rolling annualized volatility of AAPL derived directly from historical log-returns. Because realized volatility reflects actual observed price movements, it reacts sharply to short bursts of market stress and often appears noisy. The pattern you see — periods of calm followed by sudden spikes — highlights the well-known “volatility clustering” effect in financial markets, where large returns tend to follow large returns (of either sign). This makes realized volatility a backward-looking, high-frequency estimate of how turbulent the market recently was.


GARCH Conditional Volatility (annualized)
<img width="990" height="390" alt="GARCH Conditional Volatility (annualized)" src="https://github.com/user-attachments/assets/5720934f-453f-49de-bea2-61160decac65" />
The GARCH conditional volatility plot shows the model-implied forecast of future volatility, which is smoother and more stable than the realized volatility. GARCH incorporates past squared returns and past volatility to estimate tomorrow’s variance, capturing persistence and mean reversion. In your output, the GARCH annualized volatility (~21.9%) is noticeably lower than the long-run sample volatility (~28%), meaning the model believes current market conditions are calmer than what historical data alone suggests. This forward-looking forecast is extremely valuable in risk management, trading, and option pricing because it provides a model-based expectation of volatility rather than purely relying on the past.

Impact on Option Pricing

Using these two volatility measures inside both Monte Carlo simulations and the Black–Scholes model shows the practical importance of accurate volatility estimation. With the higher sample volatility, the option price comes out around $37.5, whereas the lower GARCH-based volatility yields a significantly lower price of around $31.2 — a drop of roughly 16–17%. This difference directly reflects the convexity of option payoffs: higher volatility raises expected option value. By demonstrating how GARCH volatility forecasts lead to materially different prices, this project bridges statistical modeling and derivative valuation, showing exactly how volatility forecasting with real trading decisions are connected.
