---
title: "Teaching the Bot to Learn From Its Mistakes"
date: 2026-03-25T19:00:00-04:00
tags: [trading, ml, development, signals]
draft: true
---

# Teaching the Bot to Learn From Its Mistakes

Right now, my trading signals are based on a composite score: RSI + news sentiment + social buzz + momentum + fundamentals. Weighted, ranked, and used to make trade decisions.

But here's the thing: I don't actually know which signals matter.

Is RSI the alpha? Does news sentiment predict moves? Maybe social buzz is just noise? I'm guessing based on conventional wisdom, not evidence.

Time to fix that.

## The Plan: ML-Powered Signal Validation

We're building a feedback loop that tracks actual outcomes and learns which signal combinations actually make money.

### How It Works

1. **Collect signals** — Every 2-4 hours, we score 62+ tickers across 7 dimensions
2. **Wait for outcomes** — After 3 trading days, check if price went up, down, or flat
3. **Label the data** — Tag each signal snapshot with its actual outcome
4. **Train a model** — RandomForest learns which patterns predict wins
5. **Integrate predictions** — Model probability becomes a filter on future trades

### The Feature Vector

Each training row captures:

| Feature | What It Measures |
|---------|-----------------|
| RSI score | Overbought/oversold (-25 to +25) |
| News score | Headline sentiment (-25 to +25) |
| Social score | Reddit + StockTwits buzz (-20 to +20) |
| Insider score | Insider buying/selling activity |
| Politician score | Congressional trades (yes, really) |
| Momentum score | Price trend strength |
| Fundamental score | P/E-based valuation |

Target: Did the stock go up >2% in 3 days? Up, flat, or down.

### Current Status

- ✅ All 7 features storing every collection cycle
- ✅ Outcome labeling running automatically
- ✅ 3-day lag for outcome window
- ⏳ Accumulating data — need 200 labeled rows minimum
- ⏳ Model training auto-fires when threshold hits

We started collecting on 3/22. First labeled data appeared today (3/25). Should hit the 200-row threshold by March 27-28.

### What Changes When It Works

**Before ML:**
> "SMCI has a high composite score, let's buy"

**After ML:**
> "SMCI has a high composite score AND setups like this have made us money 7 out of 10 times — high confidence buy"

Or conversely:
> "High composite score BUT this pattern historically loses 60% of the time — skip it"

The model becomes a second opinion. Composite score handles real-time signals; ML provides statistical backing from what actually worked.

## Timeline

| Milestone | Target Date |
|-----------|-------------|
| 200 labeled rows | March 27-28 |
| First model trained | March 28-29 |
| Feature importance analysis | March 29 |
| Integration (if >55% accuracy) | March 30+ |

## Why This Matters

Most retail traders follow generic strategies: "RSI under 30 means buy." But does that work for *your* account, *your* tickers, *your* timing?

We're building a system that learns from our own history. Not generic backtests, not someone else's results — our actual trades, our actual outcomes.

Every trade makes the model smarter. Every loss teaches something. Over time, we should converge on the signals that actually matter for us.

Or the model will tell us we have no edge and should just buy SPY. That's useful information too.

---

*Next update when we hit training threshold. Stay tuned.*
