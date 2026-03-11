# 🇳🇬 Nigerian Food Price Forecasting — Time Series Analysis Capstone

> TS Academy Track 3 | Personal Repository
> A time series forecasting project on Nigerian household food basket
> prices and Food CPI, using SARIMAX, GARCH, and macroeconomic drivers.

---

##  Project Overview

This project builds a data-driven forecasting system for two critical
indicators of Nigerian food affordability:

- Basket Cost — the weighted monthly cost of a standardised
  household food basket comprising 14 essential commodities
- Food CPI — Nigeria's official Consumer Price Index for food,
  published by the National Bureau of Statistics (NBS)

Using monthly data spanning January 2017 to January 2026, the
project investigates how macroeconomic shocks — particularly the
June 2023 fuel subsidy removal and naira unification — permanently
altered the structure and volatility of Nigerian food prices.

---

## Objectives

- Decompose Nigerian food price dynamics into trend, seasonal, and
  structural shock components
- Quantify the transmission of fuel price and exchange rate movements
  into food costs
- Build SARIMAX models incorporating macroeconomic exogenous regressors
  and structural break dummies
- Model time-varying forecast uncertainty using GARCH(1,1) with
  Student-t errors
- Produce a 6-month forward forecast (November 2025 – April 2026)
  for both targets


---

## Dataset

| Feature | Detail |
|---|---|
| Frequency | Monthly |
| Period | January 2017 — January 2026 |
| Observations | ~109 months |
| Targets | Basket Cost (₦), Food CPI (index) |
| Commodities | 14 (see below) |
| Macro drivers | Fuel price (₦/litre), Exchange rate (₦/USD) |

### The 14 Basket Commodities

Garri, Local Rice, Foreign Rice, Maize, Brown Beans, Yam, Potatoes,
Tomatoes, Onions, Vegetable Oil, Palm Oil, Bread, Eggs, Chicken

### Basket Cost Formula

$$\text{Basket Cost}_t = \sum_{i=1}^{14} w_i \times P_{i,t}$$

Where $w_i$ is the fixed consumption weight for commodity $i$ and
$P_{i,t}$ is the price of commodity $i$ at time $t$.

---

## Methodology

### 1. Exploratory Data Analysis
- Time series visualisation of basket cost and Food CPI
- Pre vs post-shock regime comparison (June 2023 breakpoint)
- Commodity-level price trends and weighted contribution analysis
- Rolling 12-month correlation: basket cost vs fuel price and exchange rate
- Multiplicative seasonal decomposition for both targets

### 2. Structural Break Analysis
- CUSUM test confirming June 2023 as the primary break date for basket cost
- CUSUM confirms August 2022 as the break onset for Food CPI
  (10 months earlier, reflecting Russia-Ukraine global commodity pressures)

### 3. Stationarity Testing
- ADF  tests on levels and differenced series
- ACF/PACF analysis to identify ARIMA/SARIMAX orders

### 4. Model Development

| Stage | Model | Purpose |
|---|---|---|
| Baseline | ARIMA(2,2,3) | Benchmark without seasonal or macro structure |
| Comparable | SARIMAX(2,2,3)(0,1,1,12) no exog | Adds seasonal structure only |
| Primary | SARIMAX(2,2,3)(0,1,1,12) + exog | Full model with macro drivers |
| Linked | SARIMAX(1,2,1)(0,1,0,12) + exog | Food CPI using basket cost forecasts as exog |
| Volatility | GARCH(1,1) Student-t | Time-varying uncertainty on both targets |

### 5. GARCH Volatility Modelling
- ARCH-LM test to formally confirm heteroskedasticity in residuals
- GARCH(1,1) with Student-t errors fitted to SARIMAX residuals
- Conditional volatility profiles: pre vs post-shock comparison
- IGARCH persistence confirmed for both targets (α + β ≈ 1.0)

### 6. Forecasting
- Test set evaluation: August – October 2025
- 6-month forward forecast: November 2025 – April 2026
- Exog projections using 3-month rolling average of last known values
- Confidence intervals generated from GARCH conditional volatility

---

## Key Results

### Model Performance

| Model | MSE | MAE | MAPE | Role |
|---|---|---|---|---|
| SARIMAX(2,2,3)(0,1,1,12) + exog | 11,498,346 | ₦2,814 | 2.73% | ✅ Primary — Basket Cost |
| SARIMAX(2,2,3)(0,1,1,12) no exog | 19,006,908 | ₦3,947 | 3.87% | Comparable |
| ARIMA(2,2,3) | 18,389,114 | ₦3,907 | 3.92% | Baseline |
| SARIMAX(1,2,1)(0,1,1,12) + exog | — | — | 1.16% | ✅ Primary — Food CPI |

> All primary models exceed the project target of MAPE < 5%

### GARCH Results

| Target | α (ARCH) | β (GARCH) | α + β | ν (d.f.) |
|---|---|---|---|---|
| Basket Cost | 0.8319 | 0.1681 | 1.0000 | 3.91 |
| Food CPI | — | — | ≈ 1.0000 | 4.90 |

> IGARCH persistence confirmed for both series — volatility shocks
> do not decay, consistent with a permanent structural break

### 6-Month Forward Forecasts

| Month | Basket Cost (₦) | Food CPI |
|---|---|---|
| November 2025 | ~94,500 | ~1,210 |
| December 2025 | ~93,200 | ~1,195 |
| January 2026 | ~92,800 | ~1,180 |
| February 2026 | ~92,100 | ~1,165 |
| March 2026 | ~91,600 | ~1,150 |
| April 2026 | ~91,200 | ~1,140 |

> Forecasts assume fuel price and exchange rate held at
> 3-month rolling average of last observed values.
> Wide confidence intervals apply — see notebook for full uncertainty bands.

---

## 💡 Key Findings

1. The June 2023 shock was the dominant event of the decade
Basket cost nearly tripled within 18 months of the subsidy removal.
Food CPI almost doubled. No prior event, not the 2016 recession,
the 2019 border closure, or COVID-19, produced a comparable
structural level shift.

2. Fuel price is the most important macro driver post-shock
The rolling 12-month correlation between fuel price and basket cost
rose from r = 0.27 (pre-shock) to r = 0.72 (post-shock) and remained
sustained, confirming fuel became a continuous structural driver
of food costs after subsidy removal.

3. Local staples were hit hardest, not imports
Yam (+473%), Potatoes (+349%), Local Rice (+341%), Brown Beans (+333%),
and Maize (+316%) were the top 5 most affected commodities — all
locally produced. Fuel cost transmission through transport and logistics
proved as destructive as import price channels.

4. Exogenous variables are essential — not optional
SARIMAX without exog variables forecast the wrong direction entirely
in the test period — projecting continued CPI growth while actual
Food CPI was declining. Including fuel price, exchange rate, and
shock dummies corrected both direction and magnitude.

5. Forecast uncertainty is permanent and asymmetric
IGARCH persistence (α + β = 1.0) and fat-tailed residuals (ν < 5)
in both targets confirm that post-shock Nigeria operates in a
fundamentally more uncertain food price environment. Normal-distribution
confidence intervals would systematically understate downside risk.


## Dependenciespandas
numpy
matplotlib
seaborn
statsmodels
arch
scipy
sklearn

---

## Important Notes

- Test set: Basket cost data for Nov 2025 – Jan 2026 was identified
  as forward-filled. All MAPE/MAE evaluations use the confirmed
  Aug – Oct 2025 test period only.
- Forecast assumptions: Forward projections assume constant macro
  conditions based on a 3-month rolling average. Any significant policy
  change would shift results materially.
- GARCH limitation: With ~30 post-shock observations, GARCH
  parameter estimates carry wide confidence intervals. Results are
  directionally valid but should be treated as indicative.
- Causality: High correlations throughout this project reflect
  co-movement, not established causal relationships. Models are
  optimised for forecasting accuracy, not causal identification.

---

## About This Project

This repository is a personal submission for the **TS Academy Group 7 Track 3
Capstone Project** — a group forecasting project on Nigerian food prices.

The full group project was developed collaboratively across four
sub-teams covering EDA, stationarity testing, basket price modelling,
and Food CPI forecasting. This personal repository contains the complete
end-to-end analysis independently reproduced and documented.

Programme: TS Academy — Time Series Analysis Track 3
Topic: Nigerian Food Basket Price & Food CPI Forecasting
Tools: Python, statsmodels, arch, pandas, matplotlib

---

## License

This project is submitted for educational purposes as part of the
TS Academy programme.

Dataset sourced from the National Bureau of
Statistics (NBS) Nigeria. All analysis and code are original work.
