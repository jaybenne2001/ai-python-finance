# 12. Machine learning: where AI fits

AI earns its place in finance in three areas: structured prediction, reading text at scale and speeding up analysis code.

| Approach | Inputs | Example task | Realistic expectation |
| --- | --- | --- | --- |
| Machine learning (scikit-learn, XGBoost) | Prices, fundamentals, macro data | Classify next-month up/down; predict volatility | Volatility is far more predictable than returns |
| NLP on text | Earnings calls, 10-K filings, news | Score sentiment; flag risk-factor changes | Good at summarizing and screening, weak as a lone trading signal |
| LLM-assisted analysis | Your notebook and data | Draft pandas code, explain a filing, check formulas | Fast first drafts; every number still needs verification |

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import TimeSeriesSplit

# Predict next-month volatility from recent volatility and returns
spy = returns["SPY"]
features = pd.DataFrame({
    "vol_21d": spy.rolling(21).std(),
    "vol_63d": spy.rolling(63).std(),
    "ret_21d": spy.rolling(21).sum(),
})
target = spy.rolling(21).std().shift(-21)      # the future value we want to predict
data = features.join(target.rename("target")).dropna()

X, y = data.drop(columns="target"), data["target"]
for train_idx, test_idx in TimeSeriesSplit(n_splits=5).split(X):
    model = RandomForestRegressor(n_estimators=200, random_state=0)
    model.fit(X.iloc[train_idx], y.iloc[train_idx])
    print(round(model.score(X.iloc[test_idx], y.iloc[test_idx]), 3))
```

`TimeSeriesSplit` always trains on the past and tests on the future; a random split would leak tomorrow's data into training.
