---
name: Time Series Forecasting
description: >
  Builds time series forecasts using moving averages, exponential smoothing, and
  trend-seasonal decomposition. Use this skill when you need to forecast demand,
  predict sales, time series prediction, exponential smoothing, moving average forecast,
  Holt's method, Holt-Winters, Winters' method, seasonal forecasting, trend extrapolation,
  deseasonalize data, seasonal indices, forecast accuracy, MAPE, MAE, RMSE,
  smoothing constant, forecast error, autocorrelation, runs test, random walk,
  predict next period, project future values, extrapolate a trend, demand planning,
  revenue forecast, forecast horizon, decompose time series, cyclical pattern,
  weighted moving average, alpha beta gamma, level and trend, multiplicative seasonality,
  forecast combination, simple exponential smoothing, double exponential smoothing,
  triple exponential smoothing, out-of-sample accuracy, holdout forecast test,
  forecast vs actual, time series components, noise filtering, signal extraction
---

# Time Series Forecasting

## Purpose

Decomposes time series into components (trend, seasonality, cyclical, noise), selects the appropriate forecasting method, generates point forecasts with accuracy metrics, and produces output in Excel, Python, or both.

## When to Use

- You have historical data at regular intervals and need to predict future values
- You need to choose between moving averages, exponential smoothing, or regression-based trend models
- You want to deseasonalize data and compute seasonal indices for planning
- You need forecast accuracy metrics (MAE, MAPE, RMSE) to compare methods or justify a choice
- You are testing whether a time series is random (runs test, autocorrelation) before building a model
- You need Holt-Winters forecasting for data with both trend and seasonality
- You are building a demand or revenue forecast for a business case or financial model

## Foundation

### Time Series Components

Every time series can be decomposed into up to four components:

| Component | Description | Detection Method |
|-----------|-------------|-----------------|
| **Trend (T)** | Long-run upward or downward movement | Visual inspection, regression slope |
| **Seasonality (S)** | Repeating pattern at fixed intervals (e.g., quarterly) | Autocorrelation at lag = season length |
| **Cyclical (C)** | Multi-year waves without fixed period | Requires long history; rare in short series |
| **Noise (E)** | Random variation | Residuals after removing T, S, C |

**Multiplicative model:** Y(t) = T(t) x S(t) x C(t) x E(t)
**Additive model:** Y(t) = T(t) + S(t) + C(t) + E(t)

Use multiplicative when seasonal amplitude grows with level; additive when it stays constant.

### Method Selection Guide

| Data Pattern | Recommended Method |
|-------------|-------------------|
| No trend, no seasonality, stable level | Simple exponential smoothing |
| Trend, no seasonality | Holt's double exponential smoothing |
| Trend + seasonality | Winters' triple exponential smoothing |
| Flat with noise, quick baseline | Simple moving average |
| Unknown / need benchmark | Random walk (naive forecast) |

### Core Formulas

**Simple Moving Average (SMA):**
```
F(t+1) = (1/k) * SUM(Y(t-k+1) ... Y(t))
```

**Simple Exponential Smoothing (SES):**
```
F(t+1) = alpha * Y(t) + (1 - alpha) * F(t)
```
- alpha in (0, 1). Higher alpha = more weight on recent data.

**Holt's Double Exponential Smoothing:**
```
Level:  L(t) = alpha * Y(t) + (1 - alpha) * (L(t-1) + T(t-1))
Trend:  T(t) = beta * (L(t) - L(t-1)) + (1 - beta) * T(t-1)
Forecast h steps ahead:  F(t+h) = L(t) + h * T(t)
```

**Winters' Triple Exponential Smoothing (Multiplicative):**
```
Level:    L(t) = alpha * Y(t)/S(t-s) + (1 - alpha) * (L(t-1) + T(t-1))
Trend:    T(t) = beta * (L(t) - L(t-1)) + (1 - beta) * T(t-1)
Seasonal: S(t) = gamma * Y(t)/L(t) + (1 - gamma) * S(t-s)
Forecast: F(t+h) = (L(t) + h * T(t)) * S(t-s+h)
```
- s = number of periods per season (e.g., 4 for quarterly, 12 for monthly).

**Deseasonalizing with Seasonal Indices:**
```
Seasonal Index (SI) = Actual / CMA    (CMA = centered moving average)
Deseasonalized value = Actual / SI
```
Normalize indices so they sum to s (number of seasons).

### Accuracy Metrics

```
MAE  = (1/n) * SUM(|actual - forecast|)
MAPE = (1/n) * SUM(|actual - forecast| / actual) * 100
RMSE = sqrt((1/n) * SUM((actual - forecast)^2))
```

| Metric | Strength | Watch Out |
|--------|----------|-----------|
| MAE | Interpretable in original units | Ignores scale differences across series |
| MAPE | Percentage, easy to communicate | Undefined when actual = 0; biased toward under-forecasts |
| RMSE | Penalizes large errors more heavily | Sensitive to outliers |

### Testing for Randomness

**Autocorrelation at lag k:**
```
r(k) = SUM((Y(t) - Ybar)(Y(t-k) - Ybar)) / SUM((Y(t) - Ybar)^2)
```
If r(k) at lag 1 is significantly different from zero, the series is not random -- a forecasting model can add value.

**Runs test:** Count the number of runs (consecutive sequences above or below the median). Too few runs suggests a trend; too many suggests oscillation.

### Combining Forecasts

Averaging forecasts from two or more methods often outperforms any single method. A simple average is a robust default; optimal weights can be estimated from holdout accuracy.

## Process

### Output Mode Detection

Detect the user's preferred output mode per `references/output-mode-routing.md`:

| Signal | Mode |
|--------|------|
| "Excel", "spreadsheet", "model", "workbook" | Excel |
| "Python", "script", "run", "compute" | Python |
| "both", "Excel and Python" | Both |
| "explain", "teach", "walk me through" | Teach |
| No signal | Ask user |

### Entry Mode Selection

**Guided Mode** -- The user wants step-by-step help.
1. Ask for the time series data (or file path).
2. Ask for the forecast horizon (how many periods ahead).
3. Ask which method(s) to use, or offer to recommend based on data patterns.
4. Ask for smoothing constants (alpha, beta, gamma) or whether to optimize.
5. Ask for the seasonality period if applicable.
6. Build output in the detected mode.

**Context Dump Mode** -- The user pastes data or a problem description.
1. Parse the time series values, period labels, and any stated method.
2. State what was found and any assumptions (e.g., assumed quarterly if 4 repeating peaks).
3. If the forecast horizon is missing, ask once, then build.

**Quick Draft Mode** -- The user says "just build it" or provides minimal input.
1. Take what is given. Default to SES if no trend/seasonality visible, Holt-Winters if both present.
2. Optimize smoothing constants by minimizing RMSE on the in-sample data.
3. Forecast 4 periods ahead (or 1 full season) unless told otherwise.
4. State all defaults clearly in the output.

### Adaptive Questioning

| Input | Required For | Default If Missing |
|-------|-------------|-------------------|
| Time series data | All methods | Ask -- no default |
| Forecast horizon (h) | All methods | 4 periods (or 1 season length) |
| Method | All | Auto-select based on data pattern |
| Alpha (smoothing) | SES, Holt, Winters | Optimize via RMSE minimization |
| Beta (trend smoothing) | Holt, Winters | Optimize via RMSE minimization |
| Gamma (seasonal smoothing) | Winters | Optimize via RMSE minimization |
| Seasonality period (s) | Winters, deseasonalizing | Infer from data or ask |
| Moving average window (k) | SMA | 3 periods |

### Build Steps -- Excel Mode

1. Create the workbook using Shortcut.ai API (`shortcut_excel.py`) with 5 tabs.
2. Enter all hardcoded inputs on the Assumptions tab in blue font, yellow fill.
3. Build all formulas referencing the Assumptions tab (cross-sheet links in green font).
4. Apply IB formatting per `references/excel-standards.md`.
5. Present the result with a brief interpretation of the forecast.

### Build Steps -- Python Mode

1. Write a self-contained Python script using `pandas`, `statsmodels`, and `matplotlib`.
2. Load or define the time series data.
3. Fit the selected model(s) and generate forecasts.
4. Compute accuracy metrics (MAE, MAPE, RMSE).
5. Plot actual vs. forecast with clearly labeled axes.
6. Print key results and interpretation to console.

### Build Steps -- Both Mode

1. Run the Python computation first.
2. Pass results to Shortcut.ai API to build a formatted Excel workbook.

## Excel Output Specification

### Tab 1: Raw Data

| Column | Header | Format | Content |
|--------|--------|--------|---------|
| A | Period | General | Period labels (dates, quarters, months) |
| B | Actual | #,##0.0 | Input (blue font) |

### Tab 2: Forecast Calculations

| Column | Header | Format | Content |
|--------|--------|--------|---------|
| A | Period | General | Period labels |
| B | Actual | #,##0.0 | Link to Raw Data (green font) |
| C | Level (L) | #,##0.00 | Holt/Winters level formula |
| D | Trend (T) | #,##0.00 | Holt/Winters trend formula |
| E | Seasonal (S) | 0.000 | Winters seasonal index formula |
| F | Forecast | #,##0.0 | Forecast formula (black font) |
| G | Error | #,##0.0 | =B-F |
| H | Abs Error | #,##0.0 | =ABS(G) |
| I | Pct Error | 0.0% | =H/B |

Columns C-E included only for methods that use them. SMA shows only B, F, G, H, I.

### Tab 3: Accuracy Metrics

| Cell | Label | Format | Formula |
|------|-------|--------|---------|
| B2 | MAE | #,##0.00 | =AVERAGE of Abs Error column |
| B3 | MAPE | 0.0% | =AVERAGE of Pct Error column |
| B4 | RMSE | #,##0.00 | =SQRT(AVERAGE of Error^2) |
| B6 | Method | General | Method name (blue font, input) |
| B7 | Forecast Horizon | #,##0 | h value (blue font, input) |

### Tab 4: Forecast Chart Data

| Column | Header | Format | Content |
|--------|--------|--------|---------|
| A | Period | General | All periods including forecast horizon |
| B | Actual | #,##0.0 | Actual values (blank for future periods) |
| C | Forecast | #,##0.0 | Fitted + forecast values |

This tab is the source for a line chart (actual vs. forecast).

### Tab 5: Assumptions

| Cell | Label | Default Value | Format |
|------|-------|--------------|--------|
| B2 | Method | SES / Holt / Winters / SMA | General (blue font, yellow fill) |
| B3 | Alpha | 0.30 | 0.00 (blue font, yellow fill) |
| B4 | Beta | 0.20 | 0.00 (blue font, yellow fill) |
| B5 | Gamma | 0.15 | 0.00 (blue font, yellow fill) |
| B6 | Seasonality Period (s) | 4 | #,##0 (blue font, yellow fill) |
| B7 | Moving Avg Window (k) | 3 | #,##0 (blue font, yellow fill) |
| B8 | Forecast Horizon (h) | 4 | #,##0 (blue font, yellow fill) |

Named ranges: `Alpha` -> B3, `Beta` -> B4, `Gamma` -> B5, `SeasonPeriod` -> B6, `MAWindow` -> B7, `Horizon` -> B8.

### Formatting Summary

Per `references/excel-standards.md`:
- **Blue font (0,0,255):** All input cells and all values on the Assumptions tab
- **Black font (0,0,0):** All formula cells
- **Green font (0,128,0):** Formula cells that reference a different sheet
- **Negatives:** Parentheses format -- ($1,234), never minus signs
- **Headers:** Bold, white font on navy background, bottom border
- **Freeze panes:** Row 1 on all tabs
- **Gridlines off**
- **No merged cells** in data ranges

## Python Output Specification

### Libraries

```python
import pandas as pd
import numpy as np
from statsmodels.tsa.holtwinters import ExponentialSmoothing, SimpleExpSmoothing
from statsmodels.tsa.seasonal import seasonal_decompose
import matplotlib.pyplot as plt
```

### Script Structure

1. **Data setup** -- Load from file or define inline as a pandas Series with a DatetimeIndex or PeriodIndex.
2. **Decomposition** (optional) -- `seasonal_decompose()` to visualize components.
3. **Model fitting** -- Use `SimpleExpSmoothing` for SES, `ExponentialSmoothing` for Holt/Winters.
4. **Forecasting** -- `.forecast(h)` for out-of-sample predictions.
5. **Accuracy** -- Compute MAE, MAPE, RMSE on in-sample fitted values or a holdout set.
6. **Visualization** -- Line chart with actual (blue) and forecast (orange), vertical line at forecast start.
7. **Console output** -- Print method, parameters, accuracy metrics, and forecasted values.

### Chart Specifications

- Title: "{Method} Forecast -- {Series Name}"
- X-axis: Period labels
- Y-axis: Values with comma formatting
- Legend: "Actual", "Forecast"
- Vertical dashed line separating historical from forecast periods
- Grid on, tight layout

## Output

This skill produces:

1. **Excel workbook** (`.xlsx`) with 5 tabs as specified above (Excel or Both mode)
2. **Python script** with forecast computation, accuracy metrics, and chart (Python or Both mode)
3. **Written interpretation** -- trend direction, forecast values, accuracy assessment, and method justification
4. **Teach-mode explanation** -- step-by-step walkthrough of the math with no code output (Teach mode)

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Using Winters' method on data with no seasonality | Gamma parameter fits noise as seasonal pattern, producing worse forecasts | Test for seasonality first (autocorrelation at seasonal lag); use SES or Holt if no seasonality |
| Setting alpha/beta/gamma by gut feel | Suboptimal constants degrade accuracy with no justification | Optimize by minimizing RMSE (or MAE) on in-sample or holdout data |
| Evaluating accuracy only on training data | In-sample fit always looks better than true predictive accuracy | Hold out the last s or 2s periods for out-of-sample testing |
| Forecasting 20+ periods with exponential smoothing | Exponential smoothing is a short-horizon method; long extrapolations diverge from reality | Limit forecast horizon to 1-2 seasonal cycles; use regression or econometric models for long-range |
| Using MAPE when actuals contain zeros | Division by zero produces undefined or infinite error | Use MAE or RMSE instead; or filter zero-actual periods from MAPE calculation |
| Ignoring seasonal index normalization | Un-normalized indices bias the deseasonalized series up or down | Ensure seasonal indices sum to s (number of seasons per cycle) |
| Hardcoding smoothing constants in formula cells | Cannot do sensitivity analysis; violates input/formula separation | Place alpha, beta, gamma on the Assumptions tab; reference via named ranges |
| Averaging methods with wildly different scales | If one method forecasts in units and another in thousands, the average is meaningless | Ensure all methods forecast the same target variable in the same units before combining |
| Skipping the runs test or autocorrelation check | If the series is random, any forecasting model is overfitting noise | Test for randomness first; if random, use the mean as the forecast |
| Using additive Winters' when variance grows with level | Additive model underestimates peaks and overestimates troughs at higher levels | Use multiplicative Winters' when seasonal amplitude scales with the level |

## Related Skills

- **Monte Carlo Simulation** (`simulation/monte-carlo-simulation/`) -- Pair with time series forecasting to generate probabilistic forecast ranges instead of point estimates
