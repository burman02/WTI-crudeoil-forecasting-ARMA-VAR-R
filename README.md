# WTI Crude Oil Price Forecasting — Time Series Analysis in R

**Tools:** R · tidyverse · tsibble · urca · vars · ggplot2 · stargazer

---

## Overview

This project investigates the time series properties of West Texas Intermediate 
(WTI) crude oil prices using quarterly data spanning 1980 to the present, and 
builds forecasting models of increasing complexity — from a univariate ARMA 
specification to a multivariate Vector Autoregression incorporating producer 
price inflation.

The analysis was motivated by a practical forecasting question: given the 
well-documented volatility and persistence of crude oil prices, which model 
structure best captures their dynamics? The results offer a nuanced answer — 
strong autoregressive behaviour dominates, and adding macroeconomic variables 
provides only marginal gains.

---

## Key Question

*Are WTI crude oil prices best modelled as a stochastic process driven by 
random shocks, or do they follow a deterministic trend with transitory 
deviations?*

---

## Methodology

### Part 1 — Univariate Analysis

**Stationarity Testing**  
Visual inspection of the series suggested stochastic behaviour — large, 
irregular swings with no constant mean. To formally test this, an Augmented 
Dickey-Fuller (ADF) test with a trend term was applied, reflecting the 
drifting nature of the series. Contrary to visual intuition, the ADF test 
statistic of -3.54 fell below the 5% critical value of -3.43, leading to 
rejection of the unit root null. The series is therefore trend stationary — 
an important distinction, as it implies shocks are transitory rather than 
permanent.

**Model Selection**  
ACF analysis revealed slowly decaying autocorrelations across many lags — 
characteristic of a highly persistent process inconsistent with a pure MA 
specification. Three candidate models were estimated: AR(1), AR(2), and 
ARMA(1,1). All produced statistically significant coefficients at the 1% 
level. Model selection via AIC identified ARMA(1,1) as the preferred 
specification (AIC = 1090.60 vs 1095.85 for AR(2) and 1103.85 for AR(1)), 
capturing both the long-run persistence through the AR term and the 
short-run shock dynamics through the MA term.

**Forecast Evaluation**  
The ARMA(1,1) model was used to generate four-quarter-ahead forecasts with 
95% confidence intervals. Forecast accuracy was evaluated using the Mean 
Squared Forecast Error, yielding an MSFE of 12.77 — indicating reasonable 
predictive performance given the inherent volatility of commodity prices.

### Part 2 — Multivariate Extension (VAR)

**Variable Selection**  
The Producer Price Index for All Commodities (PPIACO) was selected as the 
second variable based on a clear economic transmission mechanism: crude oil 
is a key input across production processes, so oil price movements feed 
directly into producer costs and ultimately the broader price level. This 
relationship runs in both directions — cost-push inflation can also signal 
demand conditions relevant to energy markets.

**Stationarity and Lag Selection**  
PPIACO was found to be non-stationary (ADF statistic = -2.73, above the 5% 
critical value), so its first difference was used in the VAR. Lag length 
selection via AIC, HQ and BIC criteria unanimously identified one lag as 
optimal, yielding a parsimonious VAR(1) specification.

**Estimation Results**  
The VAR(1) model showed that the lagged crude oil price is the dominant 
driver of current oil prices (coefficient = 0.947, p < 0.001), consistent 
with the strong persistence identified in Part 1. The lagged differenced PPI 
exhibited a positive but only marginally significant effect on oil prices 
(p = 0.085), suggesting weak short-run pass-through from producer inflation 
to oil prices. Notably, the residual correlation between the two equations 
was 0.846 — indicating that contemporaneous shocks to oil prices and 
producer inflation are strongly linked, even if lagged values show limited 
predictive power.

**Impulse Response Analysis**  
Impulse response functions confirmed the economic intuition behind the model. 
A shock to crude oil prices produces a persistent, slowly decaying response 
in oil prices themselves — consistent with the supply and demand dynamics 
that drive commodity markets. The response of producer price inflation to an 
oil price shock is positive but short-lived, reflecting firms' short-run 
cost adjustment before longer-run stabilisation. A shock originating in PPI 
has a negligible effect on crude oil prices, which is economically coherent — 
global oil prices are driven by international supply and demand, not domestic 
price movements.

**Granger Causality**  
Formal Granger causality tests revealed that neither variable strongly 
predicts the other in the lagged sense — though diff_ppi showed marginal 
significance at the 10% level (F = 3.00, p = 0.084). However, instantaneous 
causality was strongly rejected in both directions (χ² = 63.39, p < 0.001), 
confirming that contemporaneous shocks are highly correlated. This 
distinction matters for forecasting: the VAR's marginal gain over the 
univariate ARMA comes not from lagged predictive content but from 
contemporaneous co-movement — which is not exploitable in a pure forecasting 
context.

---

## Key Findings

- WTI crude oil prices are **trend stationary**, not a random walk — shocks 
  are transitory and the series reverts to a deterministic trend over time
- **ARMA(1,1)** is the preferred univariate model, balancing persistence 
  and short-run dynamics with the lowest AIC across all candidates
- The VAR(1) extension produces **broadly similar forecasts** to the 
  univariate model — the marginal predictive content of PPI is weak in the 
  lagged sense despite strong contemporaneous correlation
- Results suggest crude oil price forecasting is **predominantly 
  autoregressive** in nature — its own history is its best predictor

---

## Files

- `WTI-crudeoil-forecasting-ARMA-VAR-R.Rmd` — full annotated analysis
- `WTI-crudeoil-forecasting-ARMA-VAR-R.html` — knitted report with all outputs and charts
- `WTI-crudeoil-forecasting-ARMA-VAR-R.csv` — source dataset (quarterly US macroeconomic data, 
  178 observations from 1980)
- `WTI-crudeoil-forecasting-ARMA-VAR-R.pdf` — full written report

## How to Run

1. Clone this repository
2. Open `WTI-crudeoil-forecasting-ARMA-VAR-R.Rmd` in RStudio
3. Ensure `WTI-crudeoil-forecasting-ARMA-VAR-R.csv` is in the same working directory
4. Install required packages if needed:
   `install.packages(c("tidyverse", "tsibble", "urca", "vars", "stargazer"))`
5. Click Knit or run the script section by section
