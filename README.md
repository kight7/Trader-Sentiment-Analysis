# Trader Performance vs Market Sentiment
**Primetrade.ai — Data Science Internship Assignment**

---

## What I Set Out to Answer
Do traders on Hyperliquid actually behave differently on Fear days vs 
Greed days? And does that behavior affect their performance?

To find out, I merged 211,218 individual trades across 32 traders with 
2,644 days of Bitcoin sentiment data — then looked for patterns at the 
daily level.

---

## Setup
```bash
pip install pandas numpy matplotlib seaborn scikit-learn plotly
jupyter notebook analysis.ipynb
```
Run all cells top to bottom. Charts save automatically to the same folder.

---

## How I Approached It

I didn't want to just throw everything at a model and see what sticks. 
Instead I worked through it methodically:

1. Merged both datasets on date after fixing mismatched formats
2. Simplified 5 sentiment labels down to 3 — Fear, Neutral, Greed
3. Aggregated 211K trade rows into 1,692 daily trader summaries
4. Built metrics that actually matter: PnL, win rate, trade frequency, 
   position size, long ratio, and an implied leverage proxy
5. Capped outliers at the 1st/99th percentile so a handful of whale 
   trades don't distort every chart
6. Segmented traders three ways — by consistency, frequency, and size
7. Then ran KMeans clustering to see if the data would find natural 
   groupings on its own

One honest limitation upfront: the dataset has no leverage column. 
I derived an implied proxy (Size USD / position value) but acknowledge 
it's an approximation, not the real thing.

---

## What I Found

**Fear days aren't what you'd expect.**
The mean PnL is actually highest on Fear days ($5,727) — not Greed. 
But the median tells a different story: $909 on Greed vs $662 on Fear. 
What's happening is a small number of experienced traders are capitalizing 
hard on fear-driven dips, pulling the mean up. The typical trader 
does better when sentiment is positive.

**Traders get more aggressive when markets are fearful — not less.**
On Fear days, traders execute 30% more trades (122 vs 94) with 20% 
larger position sizes ($6,803 vs $5,632). This surprised me. It suggests 
the traders in this dataset are treating volatility as opportunity.

**Consistency matters more than anything else.**
Consistent traders (low PnL variance) outperform volatile traders 4.8x 
on Fear days and 2.8x on Greed days. It doesn't matter what the market 
is doing — the disciplined traders win in every regime.

**Overtrading is a trap.**
Low frequency traders match high frequency traders on Greed days 
($910 vs $907) while actually outperforming them on Fear days 
($781 vs $625). More trades = more fees, more mistakes, no extra edge.

**Large position sizing pays off — but only on Fear days.**
High size traders earn 6.3x more than low size traders on Fear days 
($2,514 vs $401). That gap shrinks significantly on Greed days. 
Big sizing is a Fear-day strategy, not a universal one.

---

## Two Strategy Recommendations

**1 — Size up on Fear, but only if you're consistent**
The data is clear: consistent traders who increase size on Fear days 
are the ones capturing the biggest returns. Volatile traders should do 
the opposite — their $430 median on Fear days doesn't justify the risk.
```
IF sentiment = Fear AND trader = Consistent → increase position size  
IF sentiment = Fear AND trader = Volatile   → reduce to minimum
```

**2 — Stop overtrading when markets get choppy**
There's no performance benefit to firing off more trades on Fear days. 
High frequency traders should cap their daily trade count at the median. 
The edge comes from trade quality, not trade quantity.
```
IF sentiment = Fear AND trader = High Frequency → cap at median trade count
```

---

## Trader Archetypes (KMeans, k=4)

Running KMeans on the trader profiles surfaced four distinct types:

| Archetype | Win Rate | Trades/Day | Avg Daily PnL |
|-----------|----------|------------|----------------|
| Whale Traders | 44% | 146 | $65,964 |
| Average Actives | 46% | 134 | $3,976 |
| Skilled HF Traders | 63% | 470 | $45,133 |
| Precision Traders | 68% | 41 | $5,670 |

The most interesting finding here: Precision Traders have the highest 
win rate (68%) while trading the least (41 trades/day). Quality over 
quantity, validated by the clustering.

---

## Honest Limitations

- 32 traders is a small sample. The directional findings are meaningful 
  but I wouldn't make production decisions without more data.
- Leverage had to be approximated — a real leverage column would 
  strengthen the analysis significantly.
- Sentiment is daily. Intraday resolution would likely reveal sharper 
  patterns around sentiment shifts.
- These segments should be recalibrated on a rolling window in any 
  live implementation — market regimes change.

---

## Charts
- chart1_performance_vs_sentiment.png
- chart2_behavior_vs_sentiment.png
- chart3_segments_heatmap.png
- chart4_elbow.png
- chart5_clusters.png