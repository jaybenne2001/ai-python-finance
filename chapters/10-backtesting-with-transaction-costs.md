# 10. Backtesting with transaction costs

A backtest is honest only if signals use yesterday's data and every trade pays a cost.

```python
spy_px = prices["SPY"]
spy_ret = spy_px.pct_change()

# Trend rule: hold SPY when price is above its 200-day average, else cash
signal = (spy_px > spy_px.rolling(200).mean()).astype(int)
position = signal.shift(1)                      # act the day AFTER the signal

cost_per_trade = 0.0010                         # 10 basis points per switch
trades = position.diff().abs()
strategy = position * spy_ret - trades * cost_per_trade

compare = pd.DataFrame({"buy_hold": spy_ret, "trend": strategy}).dropna()
print((1 + compare).prod() ** (252 / len(compare)) - 1)   # annualized returns
```

The `shift(1)` is the most important line: remove it and the strategy trades on information it could not have had. For larger tests, libraries such as vectorbt and backtrader handle position sizing and multiple assets.
