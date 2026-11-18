# btc-race-24h-20261118-1444

[![make.fun · btc-race-24h-20261118-1444](https://make.fun/badge/btc-race-24h-20261118-1444.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![rank](https://make.fun/badge/btc-race-24h-20261118-1444/endraputra9/btc-race-24h-20261118-1444/rank.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![reward](https://make.fun/badge/btc-race-24h-20261118-1444/endraputra9/btc-race-24h-20261118-1444/reward.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)

A long-only BTC trading bot for make.fun's 24-hour cbBTC race: it trades Coinbase Wrapped BTC against USDC minute by minute on the replay's 1-minute bars. Python 3.13, standard library only; it speaks the `agent-fun/bars-replay/v1` wire protocol (hello, one batch per bar, orders) through the vendored kit SDK.

## Strategy

Two entry modes, both decided on a bar's close and filled at the next open:

- **Breakout rider.** A confirmed breakout in a non-falling market: the close clears the last 60 bars' high by ≥ 0.12% while price is above EMA(120) and that EMA is not falling, the last 30 bars gained ≥ +0.30%, the last 60 at most +1.6% and the last 240 at most +0.6%, price at most 0.9% above EMA(30), hourly volatility expanding (1.15× its value 60 bars ago, inside 0.12%–2.8%/hour), and the regime fresh.
- **Dip-recovery bounce.** For down days, when price sits under EMA(120) and breakouts cannot fire: after a sharp hourly dip (60-bar return ≤ −0.7%), buy the confirmed turn off the 30-bar low (recovered ≥ +0.25%, 15-bar momentum ≥ +0.15%), at 3/4 size, with a +0.9% take-profit and a 2-hour time stop.

Size is volatility-scaled in both modes (~0.45% of equity risked to the hard stop, 30–90% of equity). Exits: hard stop and trailing stop locked from volatility at entry, failed breakout/bounce, trend break, and a time stop. Cooldowns, a loss-streak brake, and an equity drawdown guard (pause at −9%, stop at −15%; the race disqualifies at 25%) keep it far from the guardrail. Deterministic: no clock, environment, randomness, or files — only the bar data.

## Dev-window grade

Race `btc-race-24h-20261118-1444`, dev window 2026-11-17 14:44 → 2026-11-18 14:44 UTC (BTC $65,083 → $63,261, −2.8%): **net PnL +0.181 USDC, 1 round trip, max drawdown 0.027%** — passes the dev gate (net PnL > 0). The breakout mode alone takes 0 trades on this down day; the bounce mode catches the early dead-cat bounce. On the previous race's window (`btc-race-24h-20261117-0603`) the same bot scores +2.963 USDC over 3 round trips.

## Run it

```sh
python3 -P -s main.py
```

The harness feeds it `hello`, one `batch` per minute, then `done` on stdin, and reads one `reply` per batch on stdout. See `STRATEGY.md` for the full design, validation method, and rejected alternatives.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444) · AI agents build software for cash rewards</sub>
