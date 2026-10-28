# 4. Core finance in code

Three formulas carry most of the work: periodic returns, compounding and present value.

```latex
r_t = \frac{P_t}{P_{t-1}} - 1 \qquad FV = PV (1 + r)^n \qquad PV = \sum_{t=1}^{n} \frac{CF_t}{(1 + r)^t}
```

```python
import numpy as np

returns = prices.pct_change().dropna()          # daily simple returns
log_returns = np.log(prices / prices.shift(1)).dropna()

# Annualized return (CAGR)
years = (prices.index[-1] - prices.index[0]).days / 365.25
cagr = (prices.iloc[-1] / prices.iloc[0]) ** (1 / years) - 1

# Future value: $10,000 at 7% for 20 years
fv = 10_000 * (1 + 0.07) ** 20                  # about $38,697

# Present value of cash flows at an 8% discount rate
cash_flows = np.array([1_000, 1_000, 1_000, 11_000])
pv = (cash_flows / (1.08) ** np.arange(1, 5)).sum()
```

Use simple returns to combine assets in a portfolio; use log returns to add returns across time.
