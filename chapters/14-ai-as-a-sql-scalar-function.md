# 14. AI as a SQL scalar function

Warehouses now expose LLMs as functions called per row, so text enrichment runs where the data lives, under existing access controls.

| Platform | Functions | Notes |
| --- | --- | --- |
| Snowflake Cortex | `AI_COMPLETE`, `AI_CLASSIFY`, `AI_FILTER`, `AI_AGG`, `AI_SENTIMENT` | Billed per token in warehouse credits |
| Databricks | `ai_query`, `ai_classify`, `ai_analyze_sentiment`, `ai_extract` | Calls Model Serving endpoints |
| BigQuery | `AI.GENERATE`, `ML.GENERATE_TEXT` | Backed by Vertex AI models |

```sql
-- Snowflake: enrich once, persist, then query the results like any column
CREATE OR REPLACE TABLE call_signals AS
SELECT
    ticker,
    call_date,
    AI_SENTIMENT(transcript)                                   AS sentiment,
    AI_CLASSIFY(transcript, ['raised', 'maintained', 'cut'])   AS guidance
FROM earnings_calls
WHERE call_date >= '2026-01-01';          -- filter BEFORE paying per row
```

### Why the implications are underestimated

AI functions look like a small syntax feature, demoed on ten rows next to flashier chatbots. Their impact shows up in aggregate: millions of SQL users, billions of rows, every table in the warehouse.

| Shift | Before | After |
| --- | --- | --- |
| Who can do NLP | ML engineers with Python and GPUs | Anyone who writes SQL |
| What data is queryable | Structured columns; text sits unused | Text, documents and transcripts become joinable columns |
| Pipeline | Export, call API, reload, reconcile | One `SELECT`; no data leaves the platform |
| Governance | New vendor review, data copies | Inherits existing roles, masking and audit logs |
| Query logic | `WHERE` matches exact values | `AI_FILTER` matches meaning: "mentions supply-chain risk" |
| Cost model | Cents per query, scaled by data scanned | Rows × tokens; one careless query can cost thousands |
| Model risk | Deterministic SQL, reproducible | Non-deterministic outputs in systems of record |

The last two rows are why it matters for finance. Model-risk rules such as the Fed's SR 11-7 apply to a model's output wherever it lands, and an AI column inside a regulatory report is a model. Teams that treat these functions as "just SQL" will find that out in an audit.

Four rules make it production-safe:

- **Persist outputs** into tables; never put AI functions inside views that get re-queried.
- **Pin model versions**; a silent model upgrade breaks reproducibility and audits.
- **Filter first**; cost scales with rows × tokens.
- **Evaluate** against a hand-labeled sample, the way you would test any ETL step.

Function names and signatures change quickly; check each platform's current docs before building on them.
