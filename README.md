# Portfolio Risk Measurement Using VaR and CVaR

A Python project that measures the downside risk of a 30-stock portfolio with Value at Risk (VaR) and Conditional VaR (CVaR), using three methods, and then tests them on a full year of data the models never saw. The models are fitted on May 2019 to December 2023 and backtested on 2024. Prices come from Yahoo Finance.

All three methods overstated the risk in 2024. The 95% VaR was breached 1 or 2 times against 12.5 expected, and the 99% VaR was never breached against 2.5 expected.

## Key results

Daily portfolio loss estimates from the training data (May 2019 to December 2023).

| Method | VaR 95% | CVaR 95% | VaR 99% | CVaR 99% |
|---|---|---|---|---|
| Historical | -2.15% | -3.41% | -3.60% | -6.17% |
| Parametric | -2.33% | -2.94% | -3.33% | -3.82% |
| Monte Carlo | -2.33% | -2.95% | -3.33% | -3.83% |

Backtest on the 250 trading days of 2024. A breach is a day when the actual loss was worse than the VaR.

| Confidence | Method | Expected breaches | Actual breaches |
|---|---|---|---|
| 95% | Historical | 12.5 | 2 |
| 95% | Parametric | 12.5 | 1 |
| 95% | Monte Carlo | 12.5 | 1 |
| 99% | Historical | 2.5 | 0 |
| 99% | Parametric | 2.5 | 0 |
| 99% | Monte Carlo | 2.5 | 0 |

The historical method gives a much larger tail estimate at 99% (CVaR of -6.17% against -3.82% for the other two). Over the full period the portfolio's returns have fat tails, with a kurtosis of 13.8 and a skew of -0.36, and the normal distribution behind the parametric and Monte Carlo methods does not capture that.

## The portfolio

30 large U.S. stocks in three risk groups, held at fixed weights.

| Group | Weight | Stocks |
|---|---|---|
| Low risk (6% each) | 60% | JNJ, PG, KO, PEP, MCD, WMT, NEE, DUK, SO, AMT |
| Moderate risk (3% each) | 30% | JPM, BAC, GS, DIS, HD, CAT, GE, IBM, CSCO, HON |
| High risk (1% each) | 10% | NVDA, TSLA, AMD, NFLX, PYPL, CRM, UBER, ZM, SHOP, MDB |

The risk groups were assigned by judgment, not by a statistical measure.

## How it works

1. **Data.** Adjusted daily closing prices for the 30 stocks. Rows without prices for every stock are dropped, which starts the data in May 2019 because Uber and Zoom listed that year.
2. **Portfolio returns.** Daily returns are weighted into one portfolio return series.
3. **Split.** Training data runs from May 2019 to December 2023, and 2024 is held out for validation.
4. **Historical VaR and CVaR.** The 5th and 1st percentiles of training returns, and the average loss beyond each.
5. **Parametric VaR and CVaR.** A normal distribution fitted to the training mean and standard deviation.
6. **Monte Carlo VaR and CVaR.** 1,000,000 returns drawn from that same normal distribution, with a fixed seed of 42.
7. **Backtest.** Actual 2024 breaches are counted against expected breaches at both confidence levels.

## Limits

- **One test year.** The backtest covers a single year, so it shows the models were conservative in 2024, not that they would hold in a crisis. The training window includes the 2020 crash, which raises the tail estimates.
- **Normal assumption.** The parametric and Monte Carlo methods use the same normal distribution, so the Monte Carlo result mostly confirms the parametric one and is not an independent check.
- **Short history.** The usable window is about 4.6 years of training data, even though prices were requested from 2000.
- **Fixed weights and a hand-picked list.** The portfolio is a chosen set of large caps held at constant weights, with no rebalancing logic.
- **No formal backtest tests.** Breaches are counted, but no coverage test such as Kupiec's was run.

## Files

- `Portfolio_Risk_Measurement_VaR_CVaR.ipynb`: the full notebook, with code, charts, and results

## Running it

Run the notebook in Jupyter or Google Colab with an internet connection. It needs `yfinance`, `pandas`, `numpy`, `scipy`, `matplotlib`, and `seaborn`.

This is a student project and not investment advice.
