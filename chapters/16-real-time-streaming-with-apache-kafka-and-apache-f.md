# 16. Real-time streaming with Apache Kafka and Apache Flink

Kafka moves market events the moment they happen; Flink (or Spark Structured Streaming) computes on them in flight.

| Component | Job in a trading or risk system |
| --- | --- |
| Kafka topic `prices` | Every tick or bar, partitioned by ticker |
| Kafka topic `trades` | Fills from the broker or order system |
| Flink / Spark Streaming job | Rolling VaR, P&L, exposure limits per account |
| Kafka topic `alerts` | Breaches pushed to Slack, email or a dashboard |

```python
from pyspark.sql import functions as F

ticks = (spark.readStream.format("kafka")
         .option("kafka.bootstrap.servers", "broker:9092")
         .option("subscribe", "prices").load()
         .select(F.from_json(F.col("value").cast("string"),
                             "ticker STRING, price DOUBLE, ts TIMESTAMP").alias("t"))
         .select("t.*"))

bars = (ticks.withWatermark("ts", "1 minute")
        .groupBy(F.window("ts", "1 minute"), "ticker")
        .agg(F.min_by("price", "ts").alias("open"), F.max("price").alias("high"),
             F.min("price").alias("low"), F.max_by("price", "ts").alias("close")))

bars.writeStream.format("iceberg").outputMode("append").toTable("market.bars_1m")
```

The watermark tells the job how long to wait for late ticks before closing a bar — the streaming version of avoiding look-ahead bias.
