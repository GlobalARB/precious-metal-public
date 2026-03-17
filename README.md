# Gold & Silver Analysis

Ratio dynamics, cointegration-based pairs trading, and macro drivers for gold and silver.

**Author:** Alexander Ryssdal Banoun

## Overview

A comprehensive analysis of the gold-silver relationship over ~25 years, from exploratory statistics through to a fully out-of-sample walk-forward pairs trading backtest. The notebook downloads fresh data via yfinance on each run and incorporates Silver Institute supply/demand fundamentals.

## Sections

1. **Data & Setup** — Downloads gold (GC=F), silver (SI=F), plus macro assets (SPY, DXY, WTI, platinum, palladium, GDX, SLV, 10Y yield) via yfinance. Loads Silver Institute supply/demand CSV.

2. **Exploratory Data Analysis** — Return distributions, QQ plots, ADF stationarity tests on prices and returns, correlation matrices at daily/weekly/monthly frequencies.

3. **Gold/Silver Ratio Analysis** — 25-year ratio history with OLS trend and ±1σ/2σ bands. Distribution, 5-year rolling percentile rank, and regime identification (High >80, Low <55, Normal). Forward return analysis by regime.

4. **Rolling Correlations** — Multi-window (63d, 252d, 1000d) rolling correlations between gold and silver returns. Cross-asset correlation tracking vs DXY, SPY, WTI.

5. **Volatility Analysis** — Rolling realized vol comparison (63d and 252d). Silver/gold vol ratio with historical median. Silver is consistently ~1.5-2x more volatile.

6. **Cointegration & Error-Correction** — Engle-Granger cointegration test on log prices. Cointegrating regression with AR(1) half-life computed on residual levels (not returns). Rolling half-life chart.

7. **Silver Supply & Demand Fundamentals** — Silver Institute data: mine production, recycling, industrial demand, photovoltaic demand (CAGR ~16%), market balance (structural deficit since 2021).

8. **Macro Factor Correlations** — Daily return correlations vs USD, equities, oil, other precious metals, miners. Rolling 252-day correlation charts for gold/silver vs DXY and SPY.

9. **Pairs Strategy (Baseline)** — Cointegration-based pairs trading: expanding-window hedge ratio (log gold ~ beta × log silver), z-score entry/exit (±2σ entry, ±0.5σ exit), correlation regime filter (126d window, min 0.6), vol-targeting (10% ann target), 1 bps transaction costs per leg, weekly Friday execution, 1-day execution lag.

10. **Walk-Forward Backtest** — 5-year train / 1-year test walk-forward grid search over hedge-ratio mode, z-score window, entry/exit thresholds, correlation filter, and target vol. No lookahead: params selected on training data only, applied out-of-sample. Includes partial final fold to cover trailing data through present. Rolling Sharpe chart.

11. **2025-2026 Precious Metals Rally** — Deep dive into the extraordinary rally: gold +89%, silver +174%, ratio collapsing from 90 to 62. Normalized price charts, rolling 21-day correlation, monthly returns breakdown.

12. **Current Strategy Signal** — Live readout of the pairs strategy: current z-score, position direction, hedge ratio, correlation filter status, recent 63-day performance, and zoom-in charts from 2024 to present.

13. **Conclusion** — Key findings, honest assessment of OOS pairs performance (−0.27% CAGR), overlay diversification value (max DD reduced from −45% to −36%), and limitations.

## Data Requirements

| Source | Fetched Automatically | Notes |
|--------|----------------------|-------|
| Gold futures (GC=F) | Yes (yfinance) | ~25 years |
| Silver futures (SI=F) | Yes (yfinance) | ~25 years |
| SPY, DXY, WTI, PL, PA, GDX, SLV, ^TNX | Yes (yfinance) | Macro context |
| `silver_supply_and_demand.csv` | No (local file) | Silver Institute data, expected at `../silver_supply_and_demand.csv` |

The notebook degrades gracefully if the Silver S&D CSV is missing — that section simply prints a skip message.

## Setup

```bash
pip install -r requirements.txt
jupyter notebook Gold_Silver_Analysis.ipynb
```

Run cells sequentially from the top. The first code cell downloads all price data via yfinance (~30 seconds).

## Key Improvements over Original Notebook

- Added EDA section (distributions, stationarity, correlation matrices)
- Added ratio regime analysis with forward return testing
- Added cointegration test with proper half-life (AR(1) on residual levels, not returns)
- Added volatility analysis and vol ratio tracking
- Integrated silver supply/demand fundamentals (previously unused CSV)
- Added macro correlation sweep (DXY, SPY, WTI, platinum, palladium, miners)
- Fixed undefined variable bug in original ratio/trend plotting cell
- Fixed `HR_LOOKBACK` → `HR_LOOKBACK_LONG` reference error
- Fixed `ddof=0` → `ddof=1` for sample standard deviation
- Consistent strategy column names across all cells
- Improved Sharpe calculation: CAGR / annualized vol (not mean/std × √252)
- **~100× speedup** on walk-forward via vectorized expanding/anchored OLS (cumulative sums vs sklearn loop)
- Walk-forward partial final fold to cover March 2025 → March 2026 (no more gap in recent data)
- Added 2025-2026 rally analysis and current strategy signal sections
- DX-Y.NYB (delisted) replaced with UUP (Invesco DB US Dollar Bull ETF)
- Fixed `dropna(how='any')` silently killing all rows when any macro ticker had NaN

## Dependencies

pandas, numpy, matplotlib, seaborn, scipy, scikit-learn, statsmodels, yfinance, openpyxl

See `requirements.txt` for version pinning.

## License

This project is provided for educational and portfolio showcase purposes.
