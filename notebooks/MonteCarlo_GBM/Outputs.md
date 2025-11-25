<img width="989" height="390" alt="mc_hist" src="https://github.com/user-attachments/assets/8b99eacc-e94f-4069-93fb-6ab3083a19e2" />
**Monte Carlo simulation of terminal stock price under Geometric Brownian Motion (GBM)**

The plot shows the simulated distribution of the terminal stock price (St) generated using a GBM model.
It reflects the lognormal nature of stock prices — positively skewed, with most outcomes clustered near the mean but with a long right tail representing rare but large upward moves.
This distribution forms the foundation for pricing derivatives under the risk-neutral measure.


<img width="989" height="390" alt="mc_convergence" src="https://github.com/user-attachments/assets/a4cf34e2-c189-428d-a1f6-597801d732e2" />
**Monte Carlo option price convergence with increasing number of simulation paths**

This figure illustrates how the estimated option price stabilizes as the number of Monte Carlo paths increases.
The vertical bars show standard error, which decreases with more simulations, indicating lower variance and higher confidence in the price estimate.
This demonstrates the law of large numbers and validates the robustness of the Monte Carlo pricing method.


