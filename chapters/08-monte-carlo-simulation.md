# 8. Monte Carlo simulation

Monte Carlo replaces one average-return projection with thousands of possible paths, so a plan is judged by its odds of success rather than a single number.

```python
rng = np.random.default_rng(42)
n_sims, years = 10_000, 30
start, annual_draw = 1_000_000, 40_000
mean, vol = 0.06, 0.12                      # assumed real return and volatility

annual = rng.normal(mean, vol, size=(n_sims, years))
balance = np.full(n_sims, start, dtype=float)
alive = np.ones(n_sims, dtype=bool)
for t in range(years):
    balance = np.maximum(balance * (1 + annual[:, t]) - annual_draw, 0)
    alive &= balance > 0

print(f"Success rate: {alive.mean():.1%}")
print(np.percentile(balance, [10, 50, 90]).round(-3))
```

Normal returns understate crash risk. For a tougher test, resample actual historical years (bootstrapping) instead of drawing from a normal distribution.
