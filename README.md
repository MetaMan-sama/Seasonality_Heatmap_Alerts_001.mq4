# 🧠 Smart Money Index (SMI) Alert — `Seasonality_Heatmap_Alerts_001.mq4`

> **MQL4 Script for MetaTrader 4**  
> Calculates the Smart Money Index by contrasting early-session vs late-session price activity, and fires real-time alerts when sharp SMI deviations signal unusual institutional participation.

---

## Overview

The Smart Money Index (SMI) is a market internals indicator originally developed for equity markets to track **institutional vs retail activity**. The premise is rooted in a well-documented behavioral pattern:

- **Early session (9–11 AM)** — Dominated by retail and emotional traders reacting to overnight news; price moves are often erratic and frequently reversed
- **Late session (2–4 PM)** — Dominated by institutional ("smart money") participants who trade with larger size and better information; moves tend to be more directional and sustained

The SMI captures this by measuring the difference between afternoon price movement and morning price movement:

```
SMI = ΣAfternoonChange - ΣMorningChange
```

A **rising SMI** indicates institutional buying is outpacing morning retail selling. A **falling SMI** indicates institutional distribution. Sharp deviations from the previous SMI value signal meaningful shifts in smart money activity.

---

## How It Works

Every 60 seconds the script:

1. Iterates the last `LookbackPeriod` bars
2. For each bar, reads the timestamp and calculates `close - open` (price change)
3. Accumulates changes by session hour:
   - **Morning** (9:00–10:59): `sumMorning += priceChange`
   - **Afternoon** (14:00–15:59): `sumAfternoon += priceChange`
4. Computes: `SMI = sumAfternoon - sumMorning`
5. Compares to the previous cycle's SMI:

```
|currentSMI - previousSMI| >= DeviationThreshold
→ Alert: "Sharp Increase/Decrease in SMI Detected"
```

---

## Input Parameters

| Parameter | Default | Type | Description |
|---|---|---|---|
| `TradeSymbol` | `"EURUSD"` | string | Symbol to analyze |
| `Timeframe` | `PERIOD_M15` | ENUM_TIMEFRAMES | Timeframe for SMI calculation |
| `LookbackPeriod` | `14` | int | Number of bars to include in SMI calculation |
| `DeviationThreshold` | `2.0` | double | Minimum SMI change to trigger alert |
| `EnableAlerts` | `true` | bool | Trigger MT4 sound alerts |
| `EnableEmail` | `false` | bool | Send email notifications |
| `EnablePush` | `false` | bool | Send push notifications to mobile |

---

## Alert Signals

```
Sharp Increase in SMI Detected on EURUSD (Timeframe: PERIOD_M15)
Current SMI: 5.42, Previous SMI: 2.18
```
```
Sharp Decrease in SMI Detected on EURUSD (Timeframe: PERIOD_M15)
Current SMI: -3.10, Previous SMI: 0.92
```

All events are logged to the MT4 **Experts journal**.

---

## SMI Interpretation

| SMI Trend | Interpretation |
|---|---|
| Rising SMI | Smart money accumulating; institutional buying active |
| Falling SMI | Smart money distributing; institutional selling active |
| Flat SMI | No clear institutional bias |
| Sharp SMI spike | Unusual activity — potential breakout or reversal imminent |
| Sharp SMI drop | Institutional exit or reversal — caution for longs |

---

## Session Time Configuration

The script currently uses these session windows (broker server time — adjust to match your broker's GMT offset):

| Session | Hours | Activity |
|---|---|---|
| Morning (retail) | 09:00–10:59 | Early-day emotional trading |
| Afternoon (institutional) | 14:00–15:59 | Late-session smart money activity |

> **Important:** Adjust the hour comparison logic in the source if your broker's server time differs from the market local time. For NYSE/US equities, these windows align with Eastern Time. For Forex, use London/NY session overlap hours (13:00–16:00 GMT) for best results.

---

## Recommended Timeframe

| Timeframe | Notes |
|---|---|
| M15 (default) | Good granularity for session-hour resolution |
| M30 | Slightly smoother signal, fewer bars per session window |
| H1 | Fewer data points per session; less precise |

M15 is the recommended timeframe because it provides enough intraday bars to properly sample both the morning and afternoon windows within `LookbackPeriod`.

---

## Installation

1. Copy `Seasonality_Heatmap_Alerts_001.mq4` to:
   ```
   MetaTrader 4/MQL4/Scripts/
   ```
2. Restart MT4 or right-click **Navigator** → **Refresh**
3. Drag onto an M15 chart of the target symbol
4. Configure parameters and click **OK**

---

## Requirements

- MetaTrader 4 (Build 600+)
- `#property strict` compliance (enforced)
- Intraday data available with correct timestamps
- MT4 must remain running for continuous monitoring

---

## Disclaimer

This script is provided for **educational and informational purposes only**. The Smart Money Index is a derived heuristic, not a direct measurement of institutional order flow. Session-based activity patterns vary by instrument and market conditions. Always validate signals with price action and volume. Test on a demo account before live use.

---

## License

MIT License — free to use, modify, and distribute with attribution.
