# 11. Fundamental analysis with SEC EDGAR data

SEC EDGAR publishes every US public company's reported financials as free XBRL data, enough to build ratio screens without a paid vendor.

```python
import requests

headers = {"User-Agent": "Your Name your.email@example.com"}   # SEC requires a contact
cik = "0000320193"                                               # Apple
facts = requests.get(f"https://data.sec.gov/api/xbrl/companyfacts/CIK{cik}.json",
                     headers=headers).json()

rev = pd.DataFrame(facts["facts"]["us-gaap"]["RevenueFromContractWithCustomerExcludingAssessedTax"]["units"]["USD"])
annual = rev[rev["form"] == "10-K"].drop_duplicates("fy", keep="last")[["fy", "val"]]
annual["growth"] = annual["val"].pct_change()
print(annual.tail())
```

Companies use different XBRL tags for the same concept, so map tags per company before comparing. Keep the filing date: a ratio is usable in a backtest only after it was filed.
