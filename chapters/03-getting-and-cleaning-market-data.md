# 3. Getting and cleaning market data

Bad data produces confident wrong answers, so validate every series before computing anything.

| Source | Cost | Best for |
| --- | --- | --- |
| yfinance | Free | Daily prices, prototyping |
| FRED (pandas-datareader) | Free | Rates, inflation, macro series |
| SEC EDGAR | Free | Filings and XBRL fundamentals |
| Paid vendors (Polygon, Tiingo, Nasdaq Data Link) | Subscription | Intraday, clean history, delisted tickers |

```python
import pandas_datareader.data as web

rates = web.DataReader("DGS10", "fred", start="2015-01-01")   # 10-year Treasury yield

# Basic hygiene checks
print(prices.isna().sum())                         # missing values per ticker
prices = prices.ffill(limit=3)                     # fill short gaps only
jumps = prices.pct_change().abs() > 0.5            # flag 50%+ one-day moves
print(prices[jumps.any(axis=1)])                   # likely unadjusted splits or bad ticks
```

Always use split- and dividend-adjusted prices for returns; use raw prices only for what an investor actually paid.
