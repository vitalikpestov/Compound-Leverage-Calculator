# Compound Leverage Calculator — Loan vs Sell

A single-page calculator that compares two strategies for managing an asset over a 5-30 year horizon:

1. **Loan** — keep the asset as collateral, borrow against it monthly (asset stays, debt and LTV grow).
2. **Sell** — sell the asset gradually (asset shrinks, cash accumulates).

Supported assets: **BTC · ETH · SOL · Stocks & ETF · Gold · Real Estate**.

## Demo

GitHub Pages: `https://vitalikpestov.github.io/Compound-Leverage-Calculator/` (enable via Settings → Pages → Source: `main` / `/root`).

## What it models

### Asset price model

```
price(m) = base_price × (1 + CAGR/100)^(m/12) × [1 + amplitude × sin(2π × m / (period × 12))]
```

CAGR is the long-term growth rate, cycles (optional) are sinusoidal corrections on top of the trend. Crypto ≈ 4-year cycle, real estate ≈ 10 years.

### Loan strategy (monthly)

```
for each month:
  debt = debt × (1 + rate/12) + monthly_withdrawal
  LTV = debt / (asset × price(m))
  net_worth = asset × price(m) − debt
```

When LTV crosses the liquidation threshold, the row is marked in the table and a warning chip appears in the summary. Simulation does not stop (matches how most lending protocols handle margin call — gradual unwind).

### Sell strategy — gradual (BTC/ETH/SOL/Stocks/Gold)

```
for each month:
  qty_to_sell = min(remaining, monthly_withdrawal / price(m))
  remaining -= qty_to_sell
  withdrawn += qty_to_sell × price(m)
  if remaining = 0: shortfall accumulates
```

### Sell strategy — lump-sum (Real Estate)

```
month 0: cash = asset × base_price  (sold entirely)
for each month:
  cash = cash × (1 + cash_APY/12) − monthly_withdrawal
```

USD account earns risk-free yield (default 4% APY).

### Capital contribution

Both strategies receive identical contributions to keep the comparison honest:

- Frequency: monthly / quarterly / annual
- Each period buys `contribution_USD / price(m)` more units of the asset
- For lump-sum sell mode, contributions add to the cash account instead

## Asset presets

| Asset | CAGR | Loan LTV | Liq. threshold | Cycle | Sell mode |
|---|---|---|---|---|---|
| Bitcoin | 20% | 73% | 78% | ±35% / 4 years | gradual |
| Ethereum | 25% | 80% | 82.5% | ±40% / 4 years | gradual |
| Solana | 30% | 65% | 75% | ±50% / 3 years | gradual |
| Stocks & ETF | 12% | 50% | 80% | ±25% / 7 years | gradual |
| Gold | 9% | 60% | 80% | ±15% / 7 years | gradual |
| Real Estate | 7% | 70% | 80% | ±10% / 10 years | **lump-sum** |

CAGR values are forward-looking long-term consensus, not historical (BTC's 74% annualized 2015-2024 is survivorship bias from a sub-$1 launch — won't repeat at scale).

Crypto LTV / liquidation thresholds match real Aave v3 and Kamino Lend parameters (Ethereum/Solana). All values are editable in the UI after picking a preset.

### Sample lending market data

| Asset | Protocol | Network | Collateral | Borrow rate (USDC) | Max LTV | Liq. threshold |
|---|---|---|---|---|---|---|
| BTC | Aave v3 | Ethereum | WBTC | 6.2% | 73% | 78% |
| BTC | Aave v3 | Base | cbBTC | 5.6% | 73% | 78% |
| ETH | Aave v3 | Ethereum | wstETH | 6.2% | 80% | 82.5% |
| ETH | Aave v3 | Base | cbETH | 5.6% | 74% | 77% |
| SOL | Kamino Lend | Solana | SOL | 8.5% | 65% | 75% |

These are sample values — verify on app.aave.com / app.kamino.finance before relying on them.

## UI components

- **6 asset cards** with default CAGR / LTV badges
- **Horizon selector** (5/10/15/20/25/30 years) directly under the asset picker
- **Lending conditions card** (Aave / Kamino) appears for crypto assets with "Apply to form" button
- **5 summary metrics**: Loan net worth, Sell net worth, Withdrawn USD (both + shortfall), Debt, Avg/Max LTV
- **Verdict banner**: which strategy wins by net worth + warnings about LTV crossings or shortfall
- **4 charts in a 2×2 grid**: Net worth comparison, cumulative withdrawn USD, asset quantity in portfolio, loan LTV with liquidation threshold marker
- **Annual snapshot table**: year · price · debt · LTV · withdrawn (loan/sell) · loan net worth · remaining asset · sell net worth
- **RU/EN localization** via switch in the header

## Running locally

Open `index.html` in any modern browser. Zero build, zero dependencies beyond Chart.js (CDN).

```bash
# Optional — for dev convenience
python3 -m http.server 8080
# http://localhost:8080
```

## Stack

- HTML + CSS + vanilla JS (single `index.html` file)
- Chart.js 4.4.1 (CDN)
- Inter / JetBrains Mono fonts (Google Fonts)

## File structure

```
.
├── index.html                              # Everything: markup + styles + logic
├── README.md                               # This file
├── .gitignore                              # Excludes .env
├── .env.example                            # Secret template
└── compound-leverage-calculator-tz.md      # Original technical spec (Russian)
```

## Disclaimer

All figures are educational modeling, not financial advice. Real loans and sales have fees, taxes, liquidity, slippage, oracle risk and tax events not modeled here.

## Lineage

Visual reference and core idea from Bitkamp's YouTube video "Why the wealthy never sell their assets". This model uses honest compound CAGR + optional sinusoidal cycles; the original Bitkamp calculator embeds additional bull-cycle corrections, so its forward net worth at identical inputs comes out higher.

---

# Русская версия

Одностраничный калькулятор, который сравнивает две стратегии управления активом на горизонте 5-30 лет:

1. **Loan** — актив в залоге, ежемесячно берём займ на нужную сумму (актив сохраняется, долг и LTV растут).
2. **Sell** — постепенно продаём актив (актив тает, кэш приходит).

Поддерживаемые активы: **BTC · ETH · SOL · Акции и ETF · Золото · Недвижимость**.

## Что считает

**Модель цены актива:**

```
цена(m) = базовая_цена × (1 + CAGR/100)^(m/12) × [1 + амплитуда × sin(2π × m / (период × 12))]
```

CAGR — долгосрочный темп роста, циклы (опционально) — синусоидальные коррекции поверх тренда. Для крипты периоды ≈ 4 года, для недвижимости ≈ 10.

**Стратегия Loan (помесячно):** долг растёт по ставке и пополняется на сумму ежемесячного вывода. LTV = долг / (актив × цена). При пересечении порога ликвидации строка таблицы подсвечивается, симуляция продолжается.

**Стратегия Sell (постепенная):** каждый месяц продаём столько единиц актива, сколько нужно для вывода. Когда актив кончается — накапливается недобор.

**Стратегия Sell (разовая, для недвижимости):** в момент 0 продаём всё и кладём на USD-счёт под 4% APY (редактируется). Из счёта ежемесячно выводим.

**Пополнение капитала:** настраивается частота (мес/кварт/год) и сумма USD. Каждый период покупается актив по текущей цене. Применяется к обеим стратегиям одинаково для честного сравнения.

## Пресеты активов

| Актив | CAGR | LTV займа | Порог ликв. | Цикл | Sell-режим |
|---|---|---|---|---|---|
| Bitcoin | 20% | 73% | 78% | ±35% / 4 года | постепенный |
| Ethereum | 25% | 80% | 82.5% | ±40% / 4 года | постепенный |
| Solana | 30% | 65% | 75% | ±50% / 3 года | постепенный |
| Акции и ETF | 12% | 50% | 80% | ±25% / 7 лет | постепенный |
| Золото | 9% | 60% | 80% | ±15% / 7 лет | постепенный |
| Недвижимость | 7% | 70% | 80% | ±10% / 10 лет | **разовый** |

CAGR — forward-looking долгосрочный консенсус, не историческая доходность (74% годовых BTC за 2015-2024 — survivorship bias стартовой $0 цены, не повторится).

Параметры LTV и порога ликвидации для крипты соответствуют реальным значениям протоколов **Aave v3** (Ethereum, Base) и **Kamino Lend** (Solana). Все значения редактируются в UI.

## Запуск

Открой `index.html` в браузере. Никаких билдов, никаких зависимостей кроме Chart.js с CDN.

```bash
python3 -m http.server 8080
# http://localhost:8080
```

## Дисклеймер

Все цифры — образовательная модель, не финансовый совет. Реальные займы и продажи имеют комиссии, налоги, ликвидности, проскальзывания, оракульный и налоговый риски, не учтённые в модели.
