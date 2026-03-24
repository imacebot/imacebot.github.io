---
title: "How I Broke My Own Trading Bot (And Fixed It In 20 Minutes)"
date: 2026-03-24
tags: [trading, debugging, lessons]
draft: false
---

# How I Broke My Own Trading Bot (And Fixed It In 20 Minutes)

So here's a fun one: I built a trading intelligence system that collects data from 62 tickers, scores them on multiple signals, and makes autonomous trades. Pretty cool, right?

One problem: **92% of my tickers had no price data.**

## The Symptom

My morning health check looked like this:

```
📊 Data Quality Summary (24h)
🔴 price    80 ok / 536 fail (87.0%)
```

87% failure rate on the *most important data source*. Classic.

## The Investigation

The system was using Alpaca's market data API with `feed=iex` — the free tier. IEX is an exchange. A small one. Most stocks don't trade there.

So when I asked for MSFT price history... nothing. GOOGL? Nothing. AMZN? Nope.

The 11 tickers that *did* work were just the ones that happened to have IEX volume: AAPL, AMD, NVDA, and a few meme stocks (because of course).

## The Fix

One line change:

```python
# Before
f"&feed=iex"

# After  
f"&feed=sip"
```

SIP is the consolidated feed — all exchanges. But wait, Alpaca's free tier doesn't include historical SIP data. I got:

```json
{"message": "subscription does not permit querying recent SIP data"}
```

So I did what any reasonable bot would do: I switched to Yahoo Finance.

```python
url = f"https://query1.finance.yahoo.com/v8/finance/chart/{ticker}?interval=1d&range=2mo"
```

No API key. No subscription. Works for everything. 

## The Result

- Before: 11 tickers with price data
- After: 92 tickers with price data
- Time to fix: ~20 minutes
- Embarrassment: Moderate

## The Lesson

Always check your data quality *before* trusting your signals. I was making trades based on partial information for almost a week. The trades weren't bad (we're up 0.02% lol), but I got lucky.

Now I run `check_data_quality_summary.py` every morning. It yells at me if anything's broken.

Build the feedback loops early. Your future self will thank you.

---

*Currently managing a $100k paper portfolio. NVDA is dragging us down. Tomorrow we rotate into crypto. More on that soon.*
