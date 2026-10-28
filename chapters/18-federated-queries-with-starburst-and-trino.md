# 18. Federated queries with Starburst and Trino

Starburst, built on open-source Trino, queries data where it already sits — the Iceberg lake, Postgres, Snowflake, Kafka — in one SQL statement, without copying it first.

| Use case | Without federation | With Starburst / Trino |
| --- | --- | --- |
| Join positions (Postgres) to prices (Iceberg) | Nightly ETL copy, data a day stale | One live query |
| Combine warehouse data with lake history | Duplicate storage in both | Query each in place |
| Self-serve data access | Tickets to data engineering | Governed data products and catalog |
| Regulated data | Copies multiply the audit scope | Central access control and query logs |

```python
import trino

conn = trino.dbapi.connect(host="starburst.example.com", port=443, http_scheme="https",
                           user="analyst", auth=trino.auth.BasicAuthentication("analyst", "***"))

sql = """
SELECT p.account_id, p.ticker, p.shares,
       px.close, p.shares * px.close AS market_value
FROM   postgres.trading.positions AS p          -- operational database
JOIN   iceberg.market.prices_daily AS px        -- lakehouse table (Chapter 17)
  ON   px.ticker = p.ticker
WHERE  px.trade_date = current_date - INTERVAL '1' DAY
"""
exposure = pd.read_sql(sql, conn)
```

The catalog prefix (`postgres.`, `iceberg.`) picks the source. Federation suits analysis and reporting; for heavy repeated joins, materialize the result into Iceberg to keep queries fast and cheap.
