# Trader Performance vs Market Sentiment — Hyperliquid Analysis

**Data Science Intern Assignment | Primetrade.ai**

---

## Setup & How to Run

1. Open `trader_sentiment_analysis.ipynb` in **Google Colab** or Jupyter
2. Upload both CSV files: `fear_greed_index.csv` and `historical_data.csv`
3. Run all cells top to bottom (Runtime → Run All)
4. No extra installs required — only standard libraries used

---

## Methodology

### Data Preparation
- Loaded **Fear/Greed Index** (2,644 rows) and **Hyperliquid trader data** (211,224 rows)
- Normalized column names; zero missing values or duplicates in both datasets
- Converted `Timestamp IST` (DD-MM-YYYY format) to datetime and aligned both datasets at **daily granularity**
- Grouped trader records by `(account, date)` to compute per-trader daily metrics:
  - `daily_pnl` — sum of Closed PnL per day
  - `win_rate` — fraction of winning trades
  - `trades` — number of trades executed
  - `avg_size` — average trade size in USD
  - `long_short_ratio` — BUY count / SELL count (daily)
- Merged on `date` → **2,340 trader-day records** across Fear/Greed regimes

### Segmentation
Three trader segments were created from the full trader history:
1. **High Size vs Low Size** — split at median avg_size (proxy for risk appetite)
2. **Frequent vs Infrequent** — split at median total trades
3. **Consistent Winner vs Inconsistent/Loser** — positive total PnL AND win rate > 50%

### Predictive Model (Bonus)
- Random Forest Classifier to predict whether a trader-day will be profitable
- Features: sentiment encoding, trade count, avg size, win rate, long/short ratio, segment flags
- **95.9% accuracy** on held-out test set

---

## Key Insights

### Insight 1 — Fear Days Drive Higher Trade Frequency (Not Better Results)
Traders execute **~105 trades/day on Fear days vs ~77 on Greed days** — a 37% surge.  
This hyperactivity during fear does **not** improve win rates (Fear: 35.7% vs Greed: 36.3%), meaning more trades during fear = more losing trades for most participants.

### Insight 2 — Greed Days Produce Higher Median PnL
Greed days yield a **median PnL of $265 vs $123 on Fear days**.  
Despite Fear days having a nominally higher mean (driven by outlier wins), the median — a better measure for typical traders — is more than 2x higher on Greed days.

### Insight 3 — Consistent Winners Are Sentiment-Resilient
The Consistent Winner segment maintains positive PnL across both Fear and Greed regimes, while Inconsistent/Loser traders suffer disproportionately during Fear. **Skill, not sentiment, is the primary differentiator** for top performers.

---

## Strategy Recommendations

### Strategy 1 — "Fear Filter" (Infrequent & Inconsistent Traders)
> During Fear days, reduce position sizes by 30–40% and cut trade frequency.  
> Over-trading in fear-driven markets leads to net negative outcomes for most traders.  
> Exception: Consistent Winners may maintain normal exposure.

### Strategy 2 — "Greed Momentum Ride" (Frequent Traders)
> During Greed days, frequent traders can moderately increase trade count and position size.  
> Median PnL is 2x higher and win rates are slightly better.  
> Cap position sizes to manage drawdown from elevated variance seen in Extreme Greed regimes.

---

## Output Files
| File | Description |
|------|-------------|
| `trader_sentiment_analysis.ipynb` | Full annotated notebook |
| `chart1_sentiment_overview.png` | Performance metrics across all 5 sentiment levels |
| `chart2_segments.png` | Avg PnL by trader segment |
| `chart3_segment_sentiment.png` | Sentiment × segment interaction |
| `chart4_model.png` | Predictive model feature importance & accuracy |
