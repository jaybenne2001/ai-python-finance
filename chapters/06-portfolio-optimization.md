# 6. Portfolio optimization

Mean-variance optimization finds the weights with the highest return for each level of risk; the set of those portfolios is the efficient frontier.

```latex
\max_w \; \frac{w^\top \mu - r_f}{\sqrt{w^\top \Sigma w}} \quad \text{s.t.} \; \sum_i w_i = 1, \; w_i \ge 0
```

```python
from scipy.optimize import minimize

mu = returns.mean() * 252
cov = returns.cov() * 252
n = len(mu)

def neg_sharpe(w):
    return -(w @ mu - risk_free) / np.sqrt(w @ cov @ w)

res = minimize(neg_sharpe, np.ones(n) / n, method="SLSQP",
               bounds=[(0, 1)] * n,
               constraints={"type": "eq", "fun": lambda w: w.sum() - 1})
print(pd.Series(res.x, index=mu.index).round(3))
```

Optimizers amplify estimation error in expected returns. Cap position sizes, or use the PyPortfolioOpt library for shrinkage estimators and risk-parity alternatives.
