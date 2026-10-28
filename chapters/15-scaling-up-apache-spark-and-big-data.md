# 15. Scaling up: Apache Spark and big data

When data outgrows one laptop — tick data, every US equity, years of filings — the Apache ecosystem takes over from pandas.

| Project | Role | Finance use |
| --- | --- | --- |
| Apache Spark (PySpark) | Distributed compute over billions of rows | Backtests across thousands of tickers; risk aggregation |
| Apache Kafka | Real-time event streaming | Live price feeds, trade and order events |
| Apache Arrow | In-memory columnar format | Zero-copy hand-off between pandas, Spark and DuckDB |
| Apache Parquet | Compressed columnar files | Cheap storage of price history; read only the columns needed |
| Apache Iceberg | Table format with time travel | Reproduce a model on data exactly as of a past date (fixes look-ahead bias) |
| Apache Airflow | Workflow scheduling | Nightly jobs: ingest prices, recompute metrics, retrain models |

A typical pipeline runs in a straight line:

1. Kafka streams prices and trades in real time.
2. Spark lands them as Parquet in an Iceberg table.
3. Spark computes features and metrics at scale.
4. Results drop to pandas via Arrow for modeling and charts in Jupyter.
5. Airflow schedules and monitors every step.

```python
from pyspark.sql import SparkSession, functions as F
from pyspark.sql.window import Window

spark = SparkSession.builder.appName("finance").getOrCreate()
prices = spark.read.parquet("s3://bucket/daily_prices/")   # ticker, date, close

w = Window.partitionBy("ticker").orderBy("date")
returns = prices.withColumn("ret", F.col("close") / F.lag("close").over(w) - 1)

stats = (returns.groupBy("ticker")
         .agg((F.stddev("ret") * F.sqrt(F.lit(252))).alias("ann_vol"),
              (F.avg("ret") * 252).alias("ann_ret")))

stats.orderBy(F.desc("ann_vol")).limit(20).toPandas()     # Arrow speeds this hand-off
```

Rule of thumb: stay in pandas (or DuckDB/Polars) below roughly tens of GB; move to Spark when data or compute no longer fits one machine.
