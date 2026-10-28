# 7. Factor models

A factor regression splits a holding's return into market exposure (beta), style tilts and leftover skill (alpha).

```latex
R_i - R_f = \alpha + \beta_{MKT}(R_m - R_f) + \beta_{SMB} \, SMB + \beta_{HML} \, HML + \varepsilon
```

```python
import statsmodels.api as sm
import pandas_datareader.data as web

ff = web.DataReader("F-F_Research_Data_Factors_daily", "famafrench", start="2015-01-01")[0] / 100
df = returns[["QQQ"]].join(ff, how="inner")

y = df["QQQ"] - df["RF"]
X = sm.add_constant(df[["Mkt-RF", "SMB", "HML"]])
print(sm.OLS(y, X).fit().summary())
```

Read the output this way: beta near 1 tracks the market, negative HML means a growth tilt, and alpha is meaningful only if its t-statistic is above about 2.
