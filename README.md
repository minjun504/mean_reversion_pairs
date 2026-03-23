# Mean-Reversion Pairs Trading Strategy

A statistical pairs trading backtest built on limit order book (LOB) tick data 
at 10-second resolution, spanning November 2019 to December 2020.

## Strategy Overview

The strategy exploits mean-reversion in the spread between two financial instruments 
X and Y. Rather than relying on strict cointegration, the spread is constructed via 
OLS regression and its stationarity confirmed directly using the Augmented Dickey-Fuller 
(ADF) test. A rolling z-score signal is used to enter and exit positions.

## Methodology

**1. Mid-Price Construction**  
Instrument prices are estimated as the midpoint of bid and ask quotes, removing 
systematic bias from raw bid or ask prices.

**2. Cointegration Testing**  
The Engle-Granger test was applied at 1-minute and daily frequencies. Neither cleared 
the 0.05 threshold (p=0.10, p=0.079), indicating the instruments are not formally 
cointegrated.

**3. Spread Construction & Stationarity**  
The Engle-Granger procedure was decomposed manually: OLS regression of Y on X yields 
the hedge ratio; the residuals form the spread. ADF testing confirmed the spread is 
stationary (p=0.011), providing a sufficient condition for mean-reversion without 
requiring strict cointegration.

**4. Signal Generation**  
A 60-day rolling mean and standard deviation are estimated on the training period 
(Nov 2019 – Jun 2020) and fixed for out-of-sample application. Trading signals are 
generated from the z-score of the spread:
- Long entry: z < -2
- Short entry: z > +2  
- Exit: |z| < 0.5

**5. Out-of-Sample Backtest**  
Strategy evaluated on Jul – Dec 2020 with no parameter re-fitting. Transaction costs 
of 1bp per trade applied.

## Results

| Metric | Value |
|---|---|
| ADF p-value (spread) | 0.011 |
| Out-of-sample Sharpe (gross) | 2.27 |
| Out-of-sample Sharpe (net of costs) | 2.26 |
| Max Drawdown | -9.44% |
| Hit Rate | 42.93% |

The negligible difference between gross and net Sharpe confirms the strategy does 
not overtrade. Out-of-sample evaluation on an unseen period including market 
dislocation in late 2020 suggests the signal is not an artefact of overfitting.

## Limitations

- Single instrument pair over one market regime
- Fixed training window (no walk-forward validation)
- Simplified transaction cost model (no market impact or slippage)
- Standard ADF critical values used; corrected Engle-Granger critical values 
  returned p=0.079, so the mean-reversion claim is empirically rather than 
  formally justified

## Dependencies
```
pandas
numpy
scikit-learn
statsmodels
matplotlib
```

## Structure
```
├── final_data_10s.csv       # LOB tick data (not included)
└── notebook.ipynb           # Full analysis
```