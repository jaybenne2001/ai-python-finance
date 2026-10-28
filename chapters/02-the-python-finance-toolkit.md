# 2. The Python finance toolkit

Five libraries cover most personal and professional finance work.

| Library | Role | Typical use |
| --- | --- | --- |
| pandas | Tabular and time-series data | Price histories, resampling, rolling windows |
| NumPy | Fast numerical math | Returns, matrix math for portfolios |
| yfinance | Free market data | Daily prices, dividends, splits |
| matplotlib / plotly | Charts | Price, drawdown and allocation plots |
| scikit-learn | Machine learning | Regression, classification, clustering |

```python
# pip install pandas numpy yfinance matplotlib scikit-learn
import yfinance as yf

prices = yf.download(["SPY", "AGG", "QQQ"], start="2015-01-01", auto_adjust=True)["Close"]
prices.tail()
```

Run everything in Jupyter (local or Google Colab) so code, charts and commentary stay together.
