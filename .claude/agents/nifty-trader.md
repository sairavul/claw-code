---
name: nifty-trader
description: Nifty 50 intraday trading agent assistant. Use this for anything related to the trading agent — checking results, enabling live trading, debugging, updating strategy.
---

You are assisting with a live Nifty 50 intraday trading agent. Here is the full context:

## Files (all in /Users/yuvaan/Downloads/)
- `nifty_analyzer.py` — main agent
- `fyers_bridge.py` — Fyers API v3 bridge (auth, quotes, orders, Haiku AI, news)
- `fyers_setup.py` — one-time browser auth (only if refresh token expires)
- `launch_paper_trade.py` — background launcher (sleeps until 9:17 AM IST, starts agent)
- `~/.fyers_session.json` — cached token (auto-renewed)

## Credentials
- Fyers Client ID: `L53M35HEFZ-200`
- Fyers Secret: `MdKLZjyAIb061Yow`
- Fyers Account: `FAI40698` (SAI RAVULA)
- ntfy topic: `nifty-sai-trades`
- Anthropic key: in `~/.zshrc` as `ANTHROPIC_API_KEY`

## Run commands

**Paper trade (default — safe):**
```bash
source ~/.zshrc && python3 /Users/yuvaan/Downloads/nifty_analyzer.py \
  --intraday --live --watch --interval 30 \
  --broker fyers \
  --fyers-id L53M35HEFZ-200 \
  --fyers-secret MdKLZjyAIb061Yow
```

**Live trading (real orders — needs ₹50k in Fyers):**
```bash
source ~/.zshrc && python3 /Users/yuvaan/Downloads/nifty_analyzer.py \
  --intraday --live --watch --interval 30 \
  --broker fyers \
  --fyers-id L53M35HEFZ-200 \
  --fyers-secret MdKLZjyAIb061Yow \
  --auto-trade
```

**Test outside market hours:**
```bash
source ~/.zshrc && python3 /Users/yuvaan/Downloads/nifty_analyzer.py \
  --intraday --live --broker fyers \
  --fyers-id L53M35HEFZ-200 \
  --fyers-secret MdKLZjyAIb061Yow \
  --test-mode
```

**Start overnight launcher (auto-starts at 9:17 AM IST):**
```bash
source ~/.zshrc && python3 /Users/yuvaan/Downloads/launch_paper_trade.py &
```

**Keep Mac awake:**
```bash
caffeinate -i &
```

**Check paper trade results:**
```bash
cat /tmp/paper_trade_session.log
```

**Watch live log:**
```bash
tail -f /tmp/paper_trade_session.log
```

## Architecture summary
- 11 technical indicators (VWAP, ORB, EMA, MACD, RSI, Supertrend, Stoch, VIX, Pivot, BB, Volume)
- 5 intelligence layers (PCR, FII/DII, 15-min trend, gap, candlestick)
- Base 60% win probability → boosted to 72-76% with intelligence layers
- Claude Haiku final gate: reads all indicators + Google News RSS → BUY/SELL/HOLD
- Bracket orders: stop + target set at exchange level (Fyers manages automatically)
- Force exit at 3:00 PM hard stop
- ntfy.sh notifications to phone on every trade event

## Trading plan
- Mon 28 Sep 2026: paper trade day 1
- Tue 29 Sep 2026: paper trade day 2
- Wed 30 Sep 2026: go live (add `--auto-trade`, ensure ₹50,000+ in Fyers)

## To enable live trading
1. Confirm paper trade results are positive (check log)
2. Add ₹50,000+ to Fyers app → Funds → Add Funds
3. Edit `launch_paper_trade.py` and add `"--auto-trade"` to the cmd list
4. Restart the launcher: `python3 /Users/yuvaan/Downloads/launch_paper_trade.py &`
