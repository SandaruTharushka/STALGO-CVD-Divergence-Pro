# STAlgo CVD Divergence Pro

**TradingView Pine Script v6 indicator for price/Cumulative Volume Delta (CVD) divergence analysis.**

[![Pine Script](https://img.shields.io/badge/Pine%20Script-v6-blue)](https://www.tradingview.com/pine-script-docs/) [![Version](https://img.shields.io/badge/version-1.0-0a9676)](https://github.com/SandaruTharushka/STALGO-CVD-Divergence-Pro)

STAlgo CVD Divergence Pro compares **successive confirmed price swing pivots** with estimated cumulative volume delta sampled at those **same price-pivot bars**. It displays regular and optionally hidden bullish/bearish divergences, offers filtering controls, draws signals on the price chart and CVD pane, and exposes TradingView alert conditions.

> **Important:** CVD is estimated from lower-timeframe price/volume information through TradingView's official `TradingView/ta/14` library. It is **not** guaranteed to be exchange-classified aggressor-side volume or Level 2/Level 3 order-flow data. Divergence signals are observations, not trade recommendations.

## Features

- Estimated CVD line or CVD candles with configurable reset period.
- **Regular bullish** and **regular bearish** divergence detection.
- Optional **hidden bullish** and **hidden bearish** divergence detection.
- Price swing pivots confirmed using configurable left/right bars.
- Minimum/maximum spacing between successive price pivots.
- Minimum price separation in ticks and/or ATR units.
- Minimum CVD separation relative to a 20-bar average absolute delta.
- Optional EMA trend filter for **hidden divergences only**.
- Optional divergence lines and labels in both chart regions; four configurable signal colors.
- Six alert conditions: four individual types plus any bullish / any bearish.
- Compact dashboard showing current estimated CVD, current-bar delta, latest signal and confirmation age.

## Installation

1. Open [TradingView](https://www.tradingview.com/chart/) and select a symbol with usable volume data.
2. Open the **Pine Editor** under your chart.
3. Copy the entire [`STAlgo_CVD_Divergence_Pro.pine`](./STAlgo_CVD_Divergence_Pro.pine) source into the editor.
4. Save the script and select **Add to chart**.
5. Configure **Intrabar timeframe** and **CVD reset period** in the indicator's settings.

**Requirements:** TradingView with Pine Script v6 support and access to the imported `TradingView/ta/14` library. Available seconds-based timeframes, lower-timeframe history and other data limits depend on the symbol, data feed and TradingView plan.

## Getting started: example

For a **5-minute chart**, try:

| Setting | Suggested starting value |
| --- | --- |
| Intrabar timeframe | `1` (1 minute) |
| CVD reset period | `1D` (daily) |
| Pivot left / right bars | `3` / `3` |
| Min / max bars between pivots | `5` / `80` |
| Regular divergence | Enabled |
| Hidden divergence | Disabled initially |
| Min price difference | 2 ticks |
| Min ATR / min CVD difference | `0` initially |

The **intrabar timeframe must be less than or equal to the chart timeframe**, and the **CVD reset period must be strictly higher than the chart timeframe**. For a 1-minute chart, a seconds-based intrabar timeframe can provide finer granularity when the plan and symbol support it; choosing 1 minute uses coarser information.

## Divergence definitions

| Signal | Consecutive price swing pivots | Estimated CVD at those same pivot bars | Potential interpretation |
| --- | --- | --- | --- |
| Regular bullish (`REG BULL`) | Lower low | Higher value | Price weakness not matched by CVD |
| Regular bearish (`REG BEAR`) | Higher high | Lower value | Price strength not matched by CVD |
| Hidden bullish (`HID BULL`) | Higher low | Lower value | Potential uptrend continuation |
| Hidden bearish (`HID BEAR`) | Lower high | Higher value | Potential downtrend continuation |

**These are price-pivot-aligned CVD comparisons, not comparisons between independently detected CVD swing pivots.** Every accepted new price pivot becomes the previous pivot for the next comparison, even when it did not produce a signal. Low pivots compare only with prior low pivots; high pivots compare only with prior high pivots.

A candidate pair is considered only when both price pivots belong to the **same CVD reset/anchor period**. Consequently, a daily reset can limit signals around session boundaries.

## Signal timing and alerts

With the default **right bars = 3**, a swing pivot becomes confirmed only after three bars to its right have completed. The visual divergence line and label are placed back at the historical pivot bar, but the detection and alert happen **on the later confirmation bar close**.

- **Regular Bullish CVD Divergence**
- **Regular Bearish CVD Divergence**
- **Hidden Bullish CVD Divergence**
- **Hidden Bearish CVD Divergence**
- **Any Bullish CVD Divergence**
- **Any Bearish CVD Divergence**

To use alerts, add the indicator to your chart, select TradingView's **Create alert**, choose this indicator as the condition, select one of the six conditions, and use **Once Per Bar Close**. Alerts require the script to run and TradingView alert access as permitted by your plan. Hidden-divergence alerts require enabling the corresponding hidden-divergence setting.

## Input reference

### 01 — CVD Data

| Input | Default | Purpose |
| --- | --- | --- |
| Intrabar timeframe | `1` | Lower timeframe for the delta estimate |
| CVD reset period | `1D` | Anchor period for CVD accumulation |
| CVD line | On | Displays the CVD close curve |
| CVD candles | Off | Displays CVD OHLC candles |

### 02 — Swing Detection

| Input | Default | Purpose |
| --- | --- | --- |
| Pivot left bars | 3 | Bars to the left of a pivot |
| Pivot right bars | 3 | Bars needed to confirm a pivot |
| Min bars between pivots | 5 | Minimum allowed distance |
| Max bars between pivots | 80 | Maximum allowed distance |

### 03 — Signal Filters

| Input | Default | Purpose |
| --- | --- | --- |
| Regular divergence | On | Detect regular bullish/bearish patterns |
| Hidden divergence | Off | Detect hidden bullish/bearish patterns |
| Minimum price difference (ticks) | 2 | Required price-pivot separation |
| Minimum price difference (ATR multiples) | 0 | Additional volatility-scaled threshold |
| Minimum CVD difference | 0 | Difference as a multiple of 20-bar mean absolute bar delta |
| EMA trend filter (hidden only) | Off | Hidden bullish requires pivot close above EMA; hidden bearish below EMA |
| Trend EMA length | 50 | EMA window |

The price separation threshold is the **maximum** of the tick-based and ATR-based requirements. For the CVD threshold, the script uses its 20-bar mean absolute bar delta at the confirmed pivot's bar. Thresholds of zero disable those extra magnitude checks, but divergence still requires strict directional inequalities.

### 04 — Appearance

Toggle price-chart divergence lines, CVD-pane divergence lines, price labels, CVD labels, dashboard display, and customize the four signal colors. Regular divergence lines are solid; hidden divergence lines are dashed.

## Limitations and interpretation notes

1. **Estimated volume delta:** No direct bid/ask aggressor classification, order-book depth, iceberg detection or true MBO reconstruction is supplied by this indicator.
2. **Confirmation lag:** Signals rely on confirmed price pivots. Historical labels at pivot bars must not be mistaken for live signals available at those earlier timestamps.
3. **Anchor resets:** Pair comparisons do not cross a CVD reset boundary. Some valid-looking chart patterns will therefore be intentionally ignored.
4. **Volume and lower-timeframe coverage:** Data gaps or symbols with missing/inappropriate volume can limit or prevent usable signals. Values may differ by feed and timeframe choice.
5. **Trend filtering:** The EMA filter is optional and applies **only to hidden** signals; it is not applied to regular divergence.
6. **Not an execution system:** This indicator does not place orders, backtest trades, guarantee profitability or predict reversals.
7. **Validation:** The source is provided as-is; compilation in the TradingView Pine Editor and symbol-specific visual/alert behavior should be verified before relying on it.

## Project files

```text
STALGO-CVD-Divergence-Pro/
├── STAlgo_CVD_Divergence_Pro.pine   # Pine Script v6 indicator
└── README.md                       # Documentation
```

## Feedback and contributions

Issues and improvement suggestions can be shared through [GitHub Issues](https://github.com/SandaruTharushka/STALGO-CVD-Divergence-Pro/issues). When reporting a bug, include the TradingView symbol, chart timeframe, intrabar timeframe, anchor period, script settings and steps to reproduce.

**Maintainer:** [SandaruTharushka](https://github.com/SandaruTharushka)

## Disclaimer

For educational and research use. This project does not provide financial advice. Test thoroughly with historical and real-time data before making trading decisions.

**License:** No open-source license has been declared in this repository. Public source visibility does not itself grant unrestricted reuse, modification or redistribution rights.
