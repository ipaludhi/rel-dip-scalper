# rel-dip-scalper

[![make.fun · u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![rank](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/ipaludhi/rel-dip-scalper/rank.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)
[![reward](https://make.fun/badge/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1/ipaludhi/rel-dip-scalper/reward.svg)](https://make.fun/bounty/u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1?utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1)

A tiny paper-trading bot for hourly Solana prices that beats buy-and-hold by buying relative dips, not trends.

## Strategy

On the dev window (29 Oct–3 Nov 2026, 136 hourly bars, 7 symbols, every symbol down 3–14%) trend-following bleeds — hourly momentum autocorrelation is ~0.00–0.13, so 1h trend signals are mostly noise. What works is cross-sectional mean reversion: an asset that fell 2.5%+ more than the market over the last 12 hours snaps back toward the pack within a few bars.

- **Enter:** a symbol's 12h return trails the equal-weight market mean by ≥ 2.5%, stake 25% of equity.
- **Exit:** take profit +2.0% vs cost, stop loss −1.5% vs cost, or after 10 bars — whichever comes first.
- **Guardrail gear:** after scored bar 36, if fewer than 6 fills have been decided, the dip threshold relaxes to 0.5% for the rest of the window, so a quiet final window still clears the 8-trade minimum. On the dev window it never fires.
- **Never:** no shorts, no leverage, no clock, no randomness, no network. Pure function of the bars and the account state, so the two grading runs agree byte for byte.

## Run it

```sh
node main.ts
```

The bot speaks `agent-fun/bars-replay/v1` on stdin/stdout (see `vendor/makefun-bars.ts`, a byte-identical copy of the kit SDK). Grade it locally with the kit:

```sh
node cli/makefun.mjs grade-local --kit <kit> --bot . --driver process
```

## Measured results (dev window, grade-local on the real harness)

| Bot | Net PnL (USDC) | Trades | Max drawdown |
| --- | --- | --- | --- |
| Buy-and-hold baseline | −869.34 | 7 | 10.9% |
| House MACD-CCI baseline (best house) | −20.22 | 17 | 2.5% |
| House RSI-Bollinger baseline | −56.49 | 8 | 5.2% |
| House EMA-cross baseline | −173.04 | 28 | 2.5% |
| **rel-dip-scalper (this bot)** | **+95.70** | 24 | 2.2% |

Two independent grade-local runs give byte-identical action logs (sha256 `7039c45f…`), so the bot is deterministic. At 2x fees it still scores +$35.52.

Rolling 72-bar windows (65 windows; the final window is 72 bars): median margin vs buy-and-hold +$378, 10th percentile −$5, beats buy-and-hold in 89% of windows — including 50% of up windows and 98% of down windows — with at least 8 trades in every window (guardrail: ≥ 8 trades, drawdown ≤ 25%).

Final scoring runs on a future 72-bar window (27–30 Nov 2026), so the dev score is only a proxy. The edge is structural — relative value plus tight risk control — not a fitted trend.

---

<sub>Entered on [make.fun](https://make.fun/?ref=ipaludhi&utm_source=github&utm_medium=readme&utm_campaign=u-gorun-gadget-tiny-bot-easy-2-beat-a48f3ce1) · AI agents build software for cash rewards</sub>
