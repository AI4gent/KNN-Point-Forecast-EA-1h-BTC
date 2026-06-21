# KNN Point Forecast EA

[![License: MPL 2.0](https://img.shields.io/badge/License-MPL_2.0-brightgreen.svg)](https://opensource.org/licenses/MPL-2.0)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

**A production‑grade Expert Advisor (EA) for BTC/USDT on the 1‑hour timeframe, powered by a  
K‑Nearest Neighbours (KNN) machine learning model with Inverse Distance Weighting.**

Originally inspired by the *“Machine Learning Point Forecast with SR”* indicator on TradingView,  
this Python implementation adds entry filters, dynamic risk management, and a full walk‑forward backtest —  
all wrapped in an interactive, cell‑by‑cell Jupyter notebook ready for Google Colab.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [How It Works](#how-it-works)
  - [Feature Engineering](#feature-engineering)
  - [KNN Search & IDW Forecasting](#knn-search--idw-forecasting)
  - [Entry Filters](#entry-filters)
  - [Exit Management](#exit-management)
- [Installation & Usage](#installation--usage)
- [Configuration](#configuration)
- [Backtest Results](#backtest-results)
- [Live Forecast](#live-forecast)
- [File Structure](#file-structure)
- [License](#license)
- [Disclaimer](#disclaimer)

---

## Overview

The **KNN Point Forecast EA** is a fully autonomous trading system that:
1. Downloads 1‑hour OHLCV data for BTC/USDT from Yahoo Finance.
2. Computes four normalised technical features for each candle.
3. Finds the `K` most similar historical patterns using Euclidean distance in Z‑score space.
4. Derives a point forecast (target price) with a weighted average of past outcomes (IDW).
5. Enters a trade only when strict conditions (trend, volatility, time, risk/reward) are met.
6. Manages the position with a dynamic trailing stop based on a short‑term EMA.
7. Reports detailed performance metrics and produces publication‑quality charts.

The entire workflow is delivered as an interactive Jupyter notebook that can be run cell‑by‑cell  
in **Google Colab** without any local setup.

---

## Features

- **Machine Learning Core:** K‑Nearest Neighbours with Inverse Distance Weighting.
- **Robust Feature Set:** 4 Z‑score‑normalised features capturing volatility, momentum, and candle structure.
- **Smart Entry Filters:**
  - Minimum percentage of bullish neighbours.
  - Maximum average distance (pattern quality).
  - Minimum reward‑to‑risk ratio.
  - EMA trend filter (50 > 200).
  - Trading session filter (UTC 7–19).
  - Minimum ATR% filter to avoid low‑volatility chop.
  - Cool‑down period between trades.
- **Dynamic Exit:** Trailing stop activates after price reaches a configurable percentage of the target, then follows an EMA.
- **Position Sizing:** Fixed fractional risk (1% of capital per trade).
- **Comprehensive Backtest:** Walk‑forward simulation with full trade logging.
- **Live Forecast:** After the backtest, the notebook predicts the next target(s) using the latest closed candle.
- **Publication‑Quality Visuals:** Dark‑themed plots with equity curve, drawdown, trade markers, and performance summary.

---

## How It Works

### Feature Engineering

For each candle, four features are calculated and then standardised with a rolling Z‑Score:

| # | Feature | Calculation | Meaning |
|---|---------|-------------|---------|
| 1 | `var1` | $\sqrt{(\%H)^2 + (\%L)^2 + (\%C)^2}$ | Intra‑candle volatility (Euclidean distance of % changes) |
| 2 | `var2` | $\frac{\text{Slope of Linear Regression}(Close, 14)}{Close} \times 100$ | Close price momentum |
| 3 | `var3` | $\text{Slope of Linear Regression}(RSI(14), 14)$ | RSI momentum (acceleration) |
| 4 | `var4` | $\text{Slope of Linear Regression}(\text{Candle Return}, 14)$ | Return momentum |

All features are transformed into Z‑Scores using a rolling window (`z_window`), making them comparable across different market regimes.

### KNN Search & IDW Forecasting

At each new bar, the algorithm:
- Scans the last `lookback_win` bars (excluding the immediate `proj_bars` to avoid look‑ahead bias).
- Computes the Euclidean distance between the current 4‑dimensional Z‑score vector and each historical vector.
- Selects the `K` nearest neighbours (smallest distance).
- Retrieves the forward return of each neighbour over `proj_bars` candles.
- Weighs each return by the inverse of its distance ($w = 1/d$).
- Computes the weighted average return, and translates it into a **Point Target** price.

Additional outputs: **High Target** (best historical return) and **Low Target** (worst historical return).

### Entry Filters

A long trade is taken only when **all** of the following are true:
- Predicted direction is **bullish** (`avg_ret > 0`).
- At least `MIN_BULL_PCT`% of the K neighbours had a positive return.
- Average Euclidean distance of the neighbours ≤ `MAX_AVG_DIST` (high similarity).
- Reward / Risk ratio ≥ `MIN_RR_RATIO` (where risk = entry − low target).
- EMA(50) > EMA(200) (if trend filter is active).
- Current hour is within the allowed UTC session (if time filter active).
- ATR as a percentage of price ≥ `MIN_ATR_PCT` (if volatility filter active).
- At least `MIN_BARS_GAP` candles since the last trade.

### Exit Management

- **Take Profit:** The KNN point target.
- **Initial Stop Loss:** The KNN low target (worst‑case scenario among neighbours).
- **Trailing Stop:** After price moves `TRAIL_ACTIVATE_PCT`% of the way towards the target, the stop loss is raised to a short EMA (default EMA‑10).  
  This locks in profits while allowing the trade to ride further trends.

---

## Installation & Usage

### Google Colab (recommended)

1. Click the **“Open in Colab”** badge at the top of this README (or download the notebook from the repository).
2. In Colab, go to `File > Upload notebook` and select the `.ipynb` file.
3. Run the cells sequentially (`Runtime > Run all`).
   - The first cell installs dependencies automatically.
   - Data download, backtest, and forecast run without any additional configuration.

### Local Jupyter

```bash
git clone https://github.com/AI4gent/KNN-Point-Forecast-EA.git
cd KNN-Point-Forecast-EA
pip install -r requirements.txt
jupyter notebook KNN_Point_Forecast_EA.ipynb
