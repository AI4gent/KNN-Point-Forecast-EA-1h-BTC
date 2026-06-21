# KNN Point Forecast EA

[![License: MPL 2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](https://opensource.org/licenses/MPL-2.0)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

**A production‑grade Expert Advisor (EA) for BTC/USDT on the 1‑hour timeframe, powered by a  
K‑Nearest Neighbours (KNN) machine learning model with Inverse Distance Weighting.**

Originally inspired by the *“Machine Learning Point Forecast with SR”* indicator on TradingView,  
this Python implementation adds entry filters, dynamic risk management, and a full walk‑forward backtest —  
all wrapped in an interactive, cell‑by‑cell Jupyter notebook ready for Google Colab.

---

## 📊 Backtest Results (BTC/USDT 1H)

| Metric | Value |
|--------|-------|
| **Total Trades** | 40 |
| **Win Rate** | 60.0% |
| **Net Profit** | $111,529.68 |
| **Final Capital** | $121,529.68 |
| **Average Win** | $4,974.71 |
| **Average Loss** | -$491.47 |
| **Profit Factor** | **15.18** |
| **Max Drawdown** | 2.97% |
| **Sharpe Ratio (daily)** | 1.43 |

> *Achieved with default, pre‑optimised parameters on 360 days of data.  
> All trades are simulated with fixed fractional risk (1% per trade) and realistic slippage/commission.*

---

## 🔮 Latest Live Forecast

```
Bar closed at:    2026-06-21 15:00 UTC
Close price:      $64,189.97
Direction:        ▲ BULLISH
Bullish % (K=5): 100.0%
Avg Fit Distance: 1.4481
Point Target:     $64,466.86
High Target:      $64,628.57
Low Target:       $64,283.77
```

---

## ✨ Features

- **Machine Learning Core:** K‑Nearest Neighbours + Inverse Distance Weighting (IDW)
- **4 Z‑score normalised features:** Intra‑candle volatility, close momentum, RSI momentum, return momentum
- **Smart entry filters:**  
  Minimum bullish neighbour %, pattern quality (distance), reward/risk, EMA trend, trading session, ATR volatility, cool‑down gap
- **Dynamic exit:** Initial stop at worst historical return, then trails with EMA‑10 after reaching 50% of target
- **Fixed fractional risk** (default 1% per trade)
- **Comprehensive backtest** with full trade logs
- **Live forecast** after the backtest – see the next targets instantly
- **Publication‑quality dark‑theme charts**

---

## 🚀 Quick Start (Google Colab)

1. Click the **Open in Colab** badge above (or upload the notebook).
2. Run all cells (`Runtime > Run all`).
3. Dependencies are installed automatically – no local setup needed.

**Local Jupyter:**
```bash
git clone https://github.com/AI4gent/KNN-Point-Forecast-EA.git
cd KNN-Point-Forecast-EA
pip install -r requirements.txt
jupyter notebook KNN_Point_Forecast_EA.ipynb
```

---

## ⚙️ Configuration

All parameters are in **Cell 2** of the notebook and are pre‑tuned for BTC/USDT 1H.

| Category | Parameter | Default | Description |
|----------|-----------|---------|-------------|
| **Data** | `SYMBOL` | `BTC-USD` | Yahoo Finance ticker |
| | `TIMEFRAME` | `1h` | Candlestick interval |
| | `PERIOD` | `360d` | Data length |
| **Capital** | `INITIAL_CAPITAL` | 10,000 | Starting equity (USD) |
| | `RISK_PER_TRADE` | 0.01 | 1% risk per trade |
| **KNN Model** | `K_NEIGHBORS` | 5 | Number of neighbours |
| | `LOOKBACK_WIN` | 150 | Historical search window (bars) |
| | `Z_WINDOW` | 80 | Z‑score normalisation window |
| | `PROJ_BARS` | 4 | Forecast horizon (bars) |
| **Entry Filters** | `MIN_BULL_PCT` | 70 | Minimum % bullish neighbours |
| | `MAX_AVG_DIST` | 0.8 | Max average Euclidean distance |
| | `MIN_RR_RATIO` | 1.5 | Min reward/risk ratio |
| | `USE_TREND_FILTER` | True | EMA(50) > EMA(200) required |
| | `USE_TIME_FILTER` | True | Trade only during UTC 7–19 |
| | `USE_VOL_FILTER` | True | ATR% filter (≥ 0.3%) |
| | `MIN_BARS_GAP` | 8 | Min bars between trades |
| **Exit** | `TRAIL_ACTIVATE_PCT` | 50 | Trail activation threshold |
| | `TRAIL_EMA_LEN` | 10 | EMA period for trailing stop |

---

## 🧠 How It Works

1. **Feature Engineering** – Four technical features are calculated per candle and normalised with a rolling Z‑score.
2. **KNN Pattern Search** – The current Z‑score vector is compared to all historical vectors; the `K` nearest neighbours (smallest Euclidean distance) are selected.
3. **IDW Forecast** – Each neighbour’s forward return over `PROJ_BARS` candles is weighted by the inverse of its distance. The weighted average return gives the **Point Target**.
4. **Entry Logic** – A long trade is taken only if **all** filters pass.
5. **Exit Management** – Take profit = Point Target, initial stop = Low Target, then trailing stop kicks in dynamically.

---

## 📁 File Structure

```
KNN-Point-Forecast-EA/
├── KNN_Point_Forecast_EA.ipynb   # Main notebook
├── requirements.txt              # Python dependencies
└── README.md                     # This file
```

---

## 📜 License

This project is licensed under the **Mozilla Public License 2.0** – you are free to use, modify, and distribute  
the code for both private and commercial purposes. See the `LICENSE` file for details.

---

## ⚠️ Disclaimer

This software is for **educational and research purposes only**.  
It does **not** constitute financial advice. Trading cryptocurrencies carries a high level of risk.  
Always test on a demo account and never trade with money you cannot afford to lose.

---

*Built with ❤️ by an algo‑trading enthusiast.*
```
