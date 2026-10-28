# 5. Portfolio analysis and risk

Return means little without the risk taken to earn it; four metrics answer most questions.

| Metric | Question it answers | Rule of thumb |
| --- | --- | --- |
| Annualized volatility | How much does it swing? | Daily std × √252 |
| Sharpe ratio | Return per unit of risk | Above 1.0 is strong over long periods |
| Max drawdown | Worst peak-to-trough loss | The number that tests your nerve |
| Correlation | Do holdings move together? | Lower correlation = more diversification benefit |

```python
import pandas as pd

weights = pd.Series({"SPY": 0.5, "AGG": 0.3, "QQQ": 0.2})   # aligned by ticker, not position
port = (returns * weights).sum(axis=1)

vol = port.std() * np.sqrt(252)
risk_free = 0.04
sharpe = (port.mean() * 252 - risk_free) / vol

wealth = (1 + port).cumprod()
max_dd = (wealth / wealth.cummax() - 1).min()

corr = returns.corr()
```

Weights are keyed by ticker because `yf.download` returns columns sorted alphabetically, not in the order you typed them.
