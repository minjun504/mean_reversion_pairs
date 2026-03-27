# Mean-Reversion Pairs Trading Strategy

A z-score mean-reversion strategy applied to a pair of comoving instruments, backtested on limit order book tick data (10-second intervals) spanning November 2019 – December 2020.

## Overview

The strategy exploits temporary dislocations in the pricing relationship between two instruments (X and Y). When the spread between them deviates significantly from its historical mean, we bet on reversion — going long when the spread is abnormally low and short when it's abnormally high.

### Pipeline

1. **Mid-price construction** — estimate fair value as the midpoint of each instrument's best bid and ask.
2. **Train/test split** — all parameters are estimated on Nov 2019 – Jun 2020 and frozen for out-of-sample evaluation (Jul – Dec 2020).
3. **Cointegration test** — Engle–Granger test on daily mid-prices during training.
4. **Spread construction** — OLS regression of Y on X yields a hedge ratio and intercept. The spread is defined as `Y - βX - α`.
5. **Stationarity test** — ADF test on the training-period spread confirms mean-reverting behaviour.
6. **Signal generation** — the spread is normalised into a z-score using frozen rolling statistics from training. Entry at |z| > 2, exit at |z| < 0.5.
7. **Backtest** — positions are lagged by one day to prevent look-ahead bias. Returns are computed as daily spread change over pair notional value.

### Results (Out-of-Sample, Jul – Dec 2020)

| Metric | Value |
|---|---|
| Total Return | 3.36% |
| Sharpe Ratio | 1.82 |
| Max Drawdown | −0.86% |
| Hit Rate | 48.12% |
| Number of Trades | 8 |

Transaction costs (1 bps per leg) are applied separately and reported alongside gross metrics.

## Data

Limit order book snapshots at 10-second intervals with best bid/ask prices and volumes for two instruments (X and Y). The raw file (`final_data_10s.csv`) is not included in this repository.

**Schema:**

```
Time, X_BID, X_BID_VOL, X_ASK, X_ASK_VOL, Y_BID, Y_BID_VOL, Y_ASK, Y_ASK_VOL
```

## Usage

```bash
pip install pandas numpy matplotlib scikit-learn statsmodels
jupyter notebook mean_reversion_backtest.ipynb
```

## Built With

Python · pandas · NumPy · scikit-learn · statsmodels · matplotlib
