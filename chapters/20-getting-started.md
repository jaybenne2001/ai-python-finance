# 20. Getting started

Start with one notebook per question, then publish the set as a Jupyter Book course.

1. Open Google Colab or install JupyterLab locally.
2. Pull five years of prices for three to five holdings and compute returns (Chapter 4).
3. Add volatility, Sharpe and drawdown for your actual allocation (Chapter 5).
4. Try one AI task: the volatility model above, or an LLM summary of a 10-K risk-factor section.
5. Keep API keys out of notebooks: use Colab Secrets or environment variables, never hard-coded strings.
6. Group finished notebooks into chapters and build them with `jupyter-book build` for a shareable site.

Once the notebooks outgrow a laptop, move the data to Parquet and Iceberg (Chapter 17) and query it with Spark or Starburst (Chapters 15 and 18).
