# 17. The financial lakehouse: Apache Iceberg, Parquet and Arrow

The lakehouse stores all market and reference data once, as open Parquet files under Apache Iceberg tables, readable by Spark, Trino, Snowflake, DuckDB and pandas alike.

| Layer | Apache project | What it gives finance teams |
| --- | --- | --- |
| File format | Parquet | 5–10× compression; reads only the columns a query needs |
| Table format | Iceberg | ACID writes, schema evolution, snapshots and time travel |
| In-memory format | Arrow | Zero-copy transfer into pandas, Polars and DuckDB |

```sql
-- Iceberg time travel: rebuild a model on data exactly as it stood on a past date
SELECT ticker, eps, revenue
FROM finance.fundamentals
FOR TIMESTAMP AS OF TIMESTAMP '2025-12-31 23:59:59';

-- Partition prices by day so date-range queries skip irrelevant files
CREATE TABLE market.prices (ticker STRING, ts TIMESTAMP, close DOUBLE)
USING iceberg
PARTITIONED BY (days(ts));
```

Time travel is the lakehouse's best feature for finance: restated numbers no longer leak into backtests, and auditors can see exactly what a model saw.
