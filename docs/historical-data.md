# Historical Asset Returns — Data for Calibration

This document compiles publicly available historical annual returns for the six asset classes used in the calculator. It's the data backbone for:

1. **Calibrating CAGR presets** — making sure forward-looking defaults are reasonable relative to history
2. **Future "Historical Playback" feature** — replaying real annual returns instead of synthetic cycles
3. **Content material** — best/worst years, max drawdowns, volatility figures for posts

All percentages are total annual returns (price appreciation + yield/staking where applicable), nominal USD.

## Annual returns matrix (2015-2024)

Source: JP Morgan Asset Management Markets Insights / Charles Schwab Asset Allocation reports.

| Year | BTC | ETH | SOL | SPX | Gold | RE (CS HPI) |
|---|---|---|---|---|---|---|
| 2015 | +36% | — | — | +1% | -10% | +5% |
| 2016 | +120% | +753% | — | +12% | +8% | +5% |
| 2017 | +1375% | +9162% | — | +22% | +14% | +6% |
| 2018 | -74% | -82% | — | -4% | -2% | +5% |
| 2019 | +95% | -8% | — | +29% | +18% | +3% |
| 2020 | +305% | +469% | +12000% | +18% | +25% | +9% |
| 2021 | +60% | +399% | +11178% | +29% | -3% | +18% |
| 2022 | -64% | -67% | -94% | -18% | 0% | +6% |
| 2023 | +157% | +91% | +918% | +26% | +13% | +6% |
| 2024 | +120% | +47% | +27% | +25% | +27% | +4% |

**Notes:**
- SOL launched March 2020 (price ~$0.50), 2020 number reflects launch-year explosion
- ETH 2016 number reflects post-DAO recovery from a tiny base
- RE = Case-Shiller US National Home Price Index, year-over-year
- BTC, ETH, SOL include staking/yield for years where applicable

## Annualized returns (CAGR)

| Asset | 2015-2024 CAGR | 2020-2024 CAGR (recent) | Forward consensus (10yr) |
|---|---|---|---|
| BTC | 74% | 50% | 15-25% |
| ETH | 145% (9yr) | 60% | 20-30% |
| SOL | 270% (5yr) | 270% | 25-35% |
| Stocks/ETF (SPX) | 13% | 14% | 8-12% |
| Gold | 10% | 12% | 5-10% |
| Real Estate | 7% | 8% | 4-8% |

**The trap of historical CAGR for crypto:** BTC went from $0.001 (2010) to $100k+ (2024). The 74% annualized number reflects the launch-to-maturity arc and cannot be extrapolated forward. Once an asset crosses ~$2T market cap (Bitcoin's current zone), forward returns drop toward the equity risk premium + small adjustment.

**Our calculator CAGR defaults** are forward consensus:
- BTC 20%, ETH 25%, SOL 30%, Stocks 12%, Gold 9%, RE 7%

## Worst years and max drawdown

| Asset | Worst calendar year | Max drawdown (peak-to-trough) | Recovery time |
|---|---|---|---|
| BTC | -74% (2018) | -84% (Nov 2021 → Nov 2022) | ~12 months to ATH (Mar 2024) |
| ETH | -82% (2018) | -94% (Jan 2018 → Dec 2018) | ~3 years to ATH (Nov 2021) |
| SOL | -94% (2022) | -96% (Nov 2021 → Dec 2022) | ~2 years to ATH (Nov 2024) |
| Stocks/ETF | -18% (2022) | -34% (Feb 2020, COVID), -25% (Jan-Oct 2022) | ~5 months (COVID), ~9 months (2022) |
| Gold | -10% (2015) | -45% (Sep 2011 → Dec 2015) | ~5 years |
| Real Estate | -33% (2007-2012, GFC peak-to-trough) | -27% (national avg), -50%+ (worst metros) | ~7 years |

**Key implication for the calculator:** In Loan-strategy, max drawdown is what triggers liquidation. A 84% BTC drawdown with LTV starting at 40% would push LTV to ~250% — far above the 78% liquidation threshold. This is exactly what the calculator's cycle simulation is trying to surface.

## Volatility (annual standard deviation)

| Asset | Std Dev (annual) | Sharpe-style ratio (CAGR / σ) |
|---|---|---|
| BTC | ~70% | 1.06 (74/70) |
| ETH | ~95% | 1.52 (145/95) |
| SOL | ~120% | 2.25 (270/120) |
| Stocks/ETF | ~15% | 0.87 (13/15) |
| Gold | ~12% | 0.83 (10/12) |
| Real Estate | ~5% (national index) | 1.40 (7/5) |

**Note:** Sharpe ratios for crypto are inflated by survivorship-era CAGR. Forward-looking Sharpe for BTC is closer to ~0.4-0.6 (20% return / 45% vol).

## Cycle character

| Asset | Cycle length | Typical pattern |
|---|---|---|
| BTC | ~4 years | Halving-driven: bull → mania → -75% bear → 2y accumulation → repeat |
| ETH | ~4 years | Coupled to BTC but with own gas/upgrade narratives |
| SOL | ~3-4 years | Higher beta to BTC, faster mania cycles |
| Stocks/ETF | ~7-10 years | Business cycle: expansion → late-cycle → recession → recovery |
| Gold | ~7-10 years | Inverse to real yields and USD strength |
| Real Estate | ~10-18 years | Slow credit-driven cycle, geographically heterogeneous |

This is where the calculator's `cycleAmp` and `cyclePeriod` parameters come from.

## Stress scenarios for content

Useful one-liners for posts and decks:

- **BTC** "If you took a 40% LTV loan against 1 BTC at Nov 2021 top ($69k), the Dec 2022 bottom ($16k) would have pushed your LTV to ~170%. Liquidation at 78% would have happened in May 2022."
- **ETH** "ETH lost 94% from peak to trough in 2018. A 30% LTV loan would have crossed liquidation threshold in three months."
- **SOL** "SOL fell 96% in 2022. No safe LTV exists for a single-asset SOL loan through that cycle."
- **Stocks/ETF** "S&P 500 has had 11 drawdowns ≥20% since 1950. Average recovery: 18 months. Most leverage strategies survive — barely."
- **Gold** "Gold's worst year in last 50 years: -32% (1981). Max drawdown: -45% (2011-2015). Most stable of risk assets."
- **Real Estate** "GFC dropped national prices 27% peak-to-trough (2006-2012). Some metros (Phoenix, Las Vegas) lost 60%+. Recovery to old peak took until 2017-2019."

## Data sources

- **BTC, ETH, SOL annual returns:** CoinGecko, CoinMarketCap historical OHLC, end-of-year close vs prior year close
- **SPX:** S&P Dow Jones Indices, total return with dividends reinvested
- **Gold:** LBMA London PM Fix, annual change
- **Real Estate (US):** Case-Shiller US National Home Price Index (S&P CoreLogic)
- **Cross-reference:** JP Morgan Guide to the Markets, Charles Schwab "Lookback at the Markets" 2024
- **Volatility / std dev:** computed from monthly returns for each asset over 10-year window where available

Last updated: 2026-05. Refresh annually with new year-end close data.

## Future use: Historical Playback mode

Planned feature: instead of synthetic sine cycles, replay this matrix as the price path.

**Implementation sketch:**

```js
const HISTORICAL_RETURNS = {
  btc: { 2015: 0.36, 2016: 1.20, 2017: 13.75, 2018: -0.74, /* ... */ },
  // ...
};

function priceAtMonthHistorical(m, basePrice, historicalArray, startYearOffset) {
  const yearIdx = Math.floor(m/12) % historicalArray.length;
  const monthInYear = m % 12;
  const yearReturn = historicalArray[yearIdx];
  // distribute annual return geometrically across 12 months
  const monthlyMult = Math.pow(1 + yearReturn, 1/12);
  // ... compound over the path
}
```

This gives:
- **Realistic bear markets** that match actual crypto cycles
- **Margin call scenarios** at historically accurate frequencies
- **Best/worst case bracketing** by choosing different start years

UI toggle would be: `Synthetic cycles | Historical playback | No cycles (smooth CAGR)`.
