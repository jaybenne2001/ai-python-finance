# 13. NLP and LLMs on financial text

Most financial information is text. LLMs turn filings, earnings calls and news into structured fields that join to price data.

| Task | Input | Output column |
| --- | --- | --- |
| Sentiment | Earnings call transcript | Score from -1 to 1 |
| Risk-factor diff | This year's vs last year's 10-K Item 1A | New or removed risks |
| Entity extraction | News articles | Tickers, people, events |
| Guidance capture | Management commentary | Raised / maintained / cut |

```python
import anthropic, json

client = anthropic.Anthropic()    # reads ANTHROPIC_API_KEY from the environment

def classify_guidance(text: str) -> dict:
    msg = client.messages.create(
        model="claude-sonnet-4-5",
        max_tokens=200,
        messages=[{"role": "user", "content":
            "Return JSON with keys guidance (raised|maintained|cut|none) and quote.\n\n" + text}],
    )
    return json.loads(msg.content[0].text)
```

For finance-tuned sentiment without an API, FinBERT on Hugging Face is a solid free baseline. Validate any model on 100 hand-labeled examples before you trust it.
