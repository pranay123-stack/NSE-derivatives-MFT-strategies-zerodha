> ### ⚠️ Design specification — not implemented
>
> **This repository contains a design document only. There is no source code here.**
>
> Everything below describes an intended architecture. Any performance figure, benchmark,
> latency target, throughput number or Sharpe ratio in this document is a **design target
> that has never been measured**, not a result. Installation and usage instructions
> describe files that do not exist in this repository.
>
> It is published as a specification and planning artefact. For systems that are actually
> built and tested, see
> **[crypto-trading-strategies](https://github.com/pranay123-stack/crypto-trading-strategies)**.

---

# NSE Derivatives MFT Strategies - Zerodha

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![Zerodha](https://img.shields.io/badge/Zerodha-Kite-387ED1?style=flat)](https://kite.zerodha.com/)
[![NSE](https://img.shields.io/badge/NSE-F&O-FF6600?style=flat)](https://www.nseindia.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Medium-frequency trading strategies for NSE F&O segment using Zerodha Kite Connect API. Designed for retail and semi-institutional traders.

---

## Overview

| Metric | Value |
|--------|-------|
| **Timeframe** | 1min - 15min |
| **Broker** | Zerodha (Kite Connect) |
| **Instruments** | NIFTY, BANKNIFTY, Stock Options |
| **Language** | Python |

---

## Features

- **Kite Connect Integration** - Full API support for orders, positions, holdings
- **Options Strategies** - Straddle, Strangle, Iron Condor, Spreads
- **Greeks Tracking** - Real-time Delta, Gamma, Theta, Vega monitoring
- **Auto Strike Selection** - ATM/OTM selection based on premium and Greeks
- **Expiry Management** - Weekly/monthly expiry rollover automation
- **Backtesting** - Historical F&O data backtesting with realistic fills
- **Paper Trading** - Simulated trading without real orders

---

## Strategies

| Strategy | Type | Description | Risk Profile |
|----------|------|-------------|--------------|
| **Straddle Selling** | Neutral | Sell ATM CE + PE, profit from theta decay | High |
| **Strangle Selling** | Neutral | Sell OTM CE + PE, wider profit range | Medium-High |
| **Iron Condor** | Neutral | Strangle + hedges, defined risk | Medium |
| **Bull Call Spread** | Directional | Buy ATM CE, Sell OTM CE | Medium |
| **Bear Put Spread** | Directional | Buy ATM PE, Sell OTM PE | Medium |
| **Calendar Spread** | Time-based | Different expiry, same strike | Low-Medium |
| **Ratio Spread** | Skew | Unequal lots for skew plays | Medium-High |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│               NSE DERIVATIVES MFT - ZERODHA KITE                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌───────────────────────────────────────────────────────────────────┐ │
│  │                      ZERODHA KITE CONNECT                          │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐               │ │
│  │  │   Orders    │  │  Positions  │  │   Market    │               │ │
│  │  │    API      │  │    API      │  │   Data API  │               │ │
│  │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘               │ │
│  │         └────────────────┴────────┬───────┘                      │ │
│  └──────────────────────────────────┬┴──────────────────────────────┘ │
│                                     ▼                                  │
│  ┌───────────────────────────────────────────────────────────────────┐ │
│  │                      STRATEGY ENGINE                               │ │
│  │                                                                    │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │ │
│  │  │   Signal     │  │   Options    │  │    Risk      │            │ │
│  │  │  Generator   │  │   Selector   │  │   Manager    │            │ │
│  │  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘            │ │
│  │         └─────────────────┴─────────────────┘                    │ │
│  │                           │                                       │ │
│  │  ┌────────────────────────▼───────────────────────────────────┐  │ │
│  │  │                   STRATEGIES                                │  │ │
│  │  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌───────────────┐ │  │ │
│  │  │  │ Straddle │ │ Strangle │ │  Iron    │ │    Spreads    │ │  │ │
│  │  │  │  Seller  │ │  Seller  │ │ Condor   │ │ (Bull/Bear)   │ │  │ │
│  │  │  └──────────┘ └──────────┘ └──────────┘ └───────────────┘ │  │ │
│  │  └────────────────────────────────────────────────────────────┘  │ │
│  └──────────────────────────────────┬───────────────────────────────┘ │
│                                     ▼                                  │
│  ┌───────────────────────────────────────────────────────────────────┐ │
│  │                      EXECUTION & MONITORING                        │ │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │ │
│  │  │    Order     │  │   Position   │  │     P&L      │            │ │
│  │  │   Manager    │  │   Tracker    │  │   Dashboard  │            │ │
│  │  └──────────────┘  └──────────────┘  └──────────────┘            │ │
│  └───────────────────────────────────────────────────────────────────┘ │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Project Structure

```
NSE-derivatives-MFT-strategies-zerodha/
│
├── src/
│   ├── strategies/
│   │   ├── base_strategy.py
│   │   ├── straddle.py
│   │   ├── strangle.py
│   │   ├── iron_condor.py
│   │   ├── spreads.py
│   │   └── calendar.py
│   │
│   ├── broker/
│   │   ├── kite_client.py
│   │   ├── orders.py
│   │   └── positions.py
│   │
│   ├── options/
│   │   ├── greeks.py
│   │   ├── strike_selector.py
│   │   ├── iv_calculator.py
│   │   └── chain_analyzer.py
│   │
│   ├── risk/
│   │   ├── position_sizer.py
│   │   ├── stop_loss.py
│   │   └── margin_calculator.py
│   │
│   ├── data/
│   │   ├── market_data.py
│   │   ├── historical.py
│   │   └── option_chain.py
│   │
│   └── utils/
│       ├── config.py
│       ├── logger.py
│       └── notifications.py
│
├── backtest/
│   ├── engine.py
│   └── metrics.py
│
├── config/
│   └── config.yaml
│
├── tests/
├── notebooks/
├── requirements.txt
└── README.md
```

---

## Quick Start

```bash
# Clone repository
git clone https://github.com/pranay123-stack/NSE-derivatives-MFT-strategies-zerodha.git
cd NSE-derivatives-MFT-strategies-zerodha

# Install dependencies
pip install -r requirements.txt

# Configure Kite API
cp config/config.example.yaml config/config.yaml
# Add your API key and secret

# Run backtest
python -m backtest.engine --strategy straddle --index NIFTY

# Run live (paper mode)
python -m src.main --strategy iron_condor --mode paper
```

---

## Configuration

```yaml
# config/config.yaml
zerodha:
  api_key: ${KITE_API_KEY}
  api_secret: ${KITE_API_SECRET}
  user_id: ${KITE_USER_ID}

trading:
  indices:
    - NIFTY
    - BANKNIFTY
  expiry: WEEKLY  # WEEKLY or MONTHLY
  timeframe: 5m

strategy:
  name: straddle
  params:
    strike_selection: ATM
    stop_loss_percent: 30
    target_percent: 50
    entry_time: "09:20"
    exit_time: "15:15"

risk:
  max_loss_per_trade: 5000
  max_daily_loss: 15000
  position_size_lots: 1
  margin_utilization: 0.5
```

---

## Zerodha Kite Setup

1. Login to [Kite Connect Developer](https://developers.kite.trade/)
2. Create an app and get API key/secret
3. Generate access token daily using login flow
4. Add credentials to config.yaml

---

## Coming Soon

- [ ] Strategy implementations
- [ ] Kite Connect integration
- [ ] Options chain analyzer
- [ ] Greeks calculator
- [ ] Backtesting engine
- [ ] Telegram alerts

---

## Risk Warning

**F&O trading involves substantial risk.** Option selling has unlimited risk potential. Past performance does not guarantee future results. Only trade with capital you can afford to lose.

---

## License

MIT License

---

## Contact

**Pranay** - Algo Trader

[![GitHub](https://img.shields.io/badge/GitHub-pranay123--stack-181717?style=flat&logo=github)](https://github.com/pranay123-stack)
