# 9. Value at Risk and stress testing

Value at Risk (VaR) estimates the loss that should be exceeded only rarely; Expected Shortfall (CVaR) measures the average loss when it is.

| Method | How it works | Weakness |
| --- | --- | --- |
| Historical | Percentile of actual past returns | Assumes the future looks like the sample |
| Parametric | Mean and std of a normal distribution | Understates fat tails |
| Monte Carlo | Percentile of simulated returns | Only as good as the model behind it |

```python
portfolio_value = 500_000

var_95 = -np.percentile(port, 5) * portfolio_value             # 1-day historical VaR
cvar_95 = -port[port <= np.percentile(port, 5)].mean() * portfolio_value

# Stress test: replay a crisis window on today's weights
crisis = returns.loc["2020-02-19":"2020-03-23"]
crisis_loss = ((1 + (crisis * weights).sum(axis=1)).prod() - 1) * portfolio_value
print(round(var_95), round(cvar_95), round(crisis_loss))
```

VaR describes normal days; stress tests describe the days that matter. Report both.
