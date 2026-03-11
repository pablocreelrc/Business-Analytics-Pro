---
name: Demand Forecasting Pipeline
description: >
  End-to-end demand forecasting workflow that chains time series analysis, Monte Carlo
  simulation for uncertainty bounds, and optimization for resource planning. Produces a
  multi-tab workbook or Python report with forecast comparison, prediction intervals,
  optimization solution, and monitoring dashboard specs.
  Trigger phrases: "demand forecasting pipeline", "forecast demand", "predict demand",
  "demand planning workflow", "end-to-end demand forecast", "sales forecasting pipeline",
  "forecast with uncertainty", "prediction intervals", "forecast and optimize",
  "demand forecast with Monte Carlo", "simulate forecast uncertainty",
  "time series forecast with optimization", "inventory planning from forecast",
  "staffing optimization from demand", "capacity planning forecast",
  "forecast comparison", "Holt-Winters forecast", "moving average forecast",
  "seasonal demand forecast", "forecast accuracy tracking", "reforecasting triggers",
  "demand forecast monitoring", "forecast error analysis", "MAPE and MAE",
  "forecast-driven resource allocation", "demand uncertainty quantification",
  "forecast confidence intervals", "build a demand model", "forecast and plan",
  "production planning from forecast", "supply chain demand forecast"
---

# Demand Forecasting Pipeline

## Purpose

Chain three analytical skills -- time series forecasting, Monte Carlo simulation, and optimization -- into a single end-to-end workflow that takes historical demand data and produces a forecast with uncertainty bounds plus an optimized resource plan (inventory, staffing, or capacity) that accounts for forecast uncertainty.

This is a **workflow skill**, not a standalone analysis. It orchestrates three component skills in sequence, plus an exploration step at the front and a monitoring step at the end, managing data handoffs so the user gets a complete demand plan without manually stitching steps together.

## When to Use

- You have historical demand data and need to forecast future demand with rigorous uncertainty quantification
- You want to compare multiple forecasting methods (moving average, exponential smoothing, Holt-Winters, regression) and pick the best one for your data
- You need prediction intervals around the point forecast, not just a single number
- Forecast results need to drive a downstream resource decision: how much inventory to hold, how many staff to schedule, how much capacity to build
- You want to set up a monitoring framework that detects when the forecast has gone stale and needs updating
- Leadership asks "what should we expect demand to be, how confident are we, and what should we do about it?"
- You need to present a demand plan to operations, finance, or supply chain stakeholders

## Workflow Architecture

```
+-----------------------------------------------------------------+
|               DEMAND FORECASTING PIPELINE                        |
|                                                                  |
|  +----------------+    +-------------------+                     |
|  |  STEP 1         |    |  STEP 2            |                    |
|  |  Explore the    |--->|  Build Forecast    |                    |
|  |  Data           |    |  Models            |                    |
|  +----------------+    +-------------------+                     |
|        |                       |                                  |
|        | Patterns, outliers,   | Point forecasts,                |
|        | decomposition          | accuracy metrics                |
|        |                       |                                  |
|        v                       v                                  |
|                        +-------------------+                     |
|                        |  STEP 3            |                    |
|                        |  Quantify Forecast |                    |
|                        |  Uncertainty        |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                | Prediction intervals,           |
|                                | probability distributions        |
|                                v                                  |
|                        +-------------------+                     |
|                        |  STEP 4            |                    |
|                        |  Optimize Resource |                    |
|                        |  Allocation         |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                | Optimal inventory/staffing/     |
|                                | capacity plan                    |
|                                v                                  |
|                        +-------------------+                     |
|                        |  STEP 5            |                    |
|                        |  Monitor and       |                    |
|                        |  Update            |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                v                                  |
|                        +-------------------+                     |
|                        |  DELIVERABLE       |                    |
|                        |  Multi-tab Excel   |                    |
|                        |  or Python report   |                    |
|                        +-------------------+                     |
+-----------------------------------------------------------------+
```

### Step-Level Inputs and Outputs

| Step | Skill | Inputs | Outputs | Handoff to Next |
|------|-------|--------|---------|-----------------|
| 1 | `forecasting/time-series-forecasting` (concepts) | Historical demand data, business context | Decomposition (trend, seasonality, residuals), outlier flags, data quality assessment | Clean data and pattern insights feed model selection in Step 2 |
| 2 | `forecasting/time-series-forecasting` | Clean data from Step 1, candidate methods | Point forecasts from multiple methods, accuracy metrics (MAPE, MAE, RMSE), best model selection | Best-model forecast feeds uncertainty quantification in Step 3 |
| 3 | `simulation/monte-carlo-simulation` | Best forecast from Step 2, historical forecast errors | Prediction intervals at multiple confidence levels, demand probability distribution per period | Demand distribution feeds optimization in Step 4 |
| 4 | `optimization/optimization-models` | Demand distribution from Step 3, cost parameters (holding, shortage, hiring, etc.) | Optimal resource plan (order quantities, staffing levels, capacity) with expected cost | Resource plan feeds monitoring setup in Step 5 |
| 5 | Monitoring framework | Forecast vs. actuals tracking, reforecasting trigger conditions | Dashboard specification, alert thresholds, reforecast schedule | Final deliverable |

## Process

### Entry Mode Selection

When the user invokes this workflow, determine which entry mode applies:

**Guided Mode** -- User says something like "help me forecast demand" or "walk me through demand planning." Run each step interactively, explaining the patterns found, confirming the best model, and reviewing the optimization setup before solving.

**Context Dump Mode** -- User provides historical data, cost parameters, and constraints upfront. Run all five steps in sequence, present the final workbook with a summary of decisions made at each stage.

**Quick Draft Mode** -- User says "just forecast this demand data" or provides a data series inline. Use what is given, fill reasonable defaults (state them clearly), produce the complete deliverable immediately.

### Output Mode Detection

Detect the user's preferred output mode per `references/output-mode-routing.md`:

| Mode | What Happens |
|------|-------------|
| Excel | Shortcut.ai builds an IB-formatted multi-tab workbook |
| Python | Self-contained script with pandas/statsmodels for forecasting, numpy/scipy for simulation, PuLP for optimization, matplotlib for charts |
| Both | Python computes, Shortcut.ai formats results into Excel |
| Teach | Walk through the framework and math, no code output |

---

### Step 1: Explore the Data

**Invoke:** `forecasting/time-series-forecasting` (exploration concepts)

**Purpose in this workflow:** Understand the structure of the demand data before building models. Identify trend, seasonality, outliers, and data quality issues that will affect model selection and accuracy.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Historical demand data (time series) | Yes | Ask -- no default |
| Time granularity (daily, weekly, monthly, quarterly) | Yes | Infer from data |
| Business context (what is being forecasted) | Yes | Ask -- no default |
| Known events or structural breaks | No | None assumed |

**Actions:**
1. Plot the raw time series to visually inspect for trend, seasonality, and outliers
2. Decompose the series into trend, seasonal, and residual components (additive or multiplicative)
3. Check for stationarity (visual inspection of residuals, note any obvious non-stationarity)
4. Identify and flag outliers -- determine whether they are data errors or real events
5. Assess data quality: missing values, frequency gaps, sufficient history for the chosen methods
6. Determine the appropriate forecast horizon based on business need and data characteristics

**Output carried forward:**
- Data quality assessment: clean/needs treatment, missing values handled
- Decomposition results: trend direction, seasonal pattern (period and amplitude), residual characteristics
- Outlier flags and treatment decisions
- Recommended model candidates based on data patterns

**Guided mode checkpoint:** "Data exploration complete. The series shows [upward/downward/flat] trend with [monthly/quarterly/no] seasonality of amplitude [X]. I found [N] outliers which I've [treated/flagged]. Based on these patterns, I recommend comparing [Method A], [Method B], and [Method C]. Ready to build forecast models?"

---

### Step 2: Build Forecast Models

**Invoke:** `forecasting/time-series-forecasting`

**Purpose in this workflow:** Apply multiple forecasting methods, measure their accuracy on held-out data, and select the best model. This step produces the point forecast that will be wrapped with uncertainty bounds in Step 3.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Clean data from Step 1 | Yes | From prior step |
| Forecast horizon (number of periods ahead) | Yes | Ask -- no default |
| Methods to compare | No | Based on Step 1 patterns |
| Hold-out period for validation | No | Last 20% of data |

**Candidate methods by data pattern:**

| Pattern | Recommended Methods |
|---------|-------------------|
| No trend, no seasonality | Simple Moving Average, Simple Exponential Smoothing |
| Trend, no seasonality | Double Exponential Smoothing (Holt), Linear Regression |
| Trend + seasonality | Holt-Winters (additive or multiplicative), Seasonal Regression |
| Complex / many predictors | Multiple Regression with seasonal dummies, ARIMA |

**Actions:**
1. Split data into training set and hold-out validation set
2. Fit each candidate method on the training set
3. Generate forecasts for the hold-out period from each method
4. Calculate accuracy metrics for each method:
   - **MAE** (Mean Absolute Error): average absolute deviation
   - **MAPE** (Mean Absolute Percentage Error): percentage-based accuracy
   - **RMSE** (Root Mean Squared Error): penalizes large errors more heavily
   - **Tracking Signal**: cumulative error / MAD -- detects systematic bias
5. Select the best model based on hold-out accuracy (lowest MAPE or MAE)
6. Re-fit the best model on the full dataset and produce the final point forecast
7. Record the historical forecast errors (actual - forecast) for use in Step 3

**Output carried forward:**
- Point forecast for each future period (best model)
- Model accuracy metrics (MAPE, MAE, RMSE) for the selected model
- Historical forecast errors (actual - predicted) for the validation period
- Comparison table of all methods tested
- These feed uncertainty quantification in Step 3

**Guided mode checkpoint:** "Model comparison complete. [Method A] achieved MAPE of [X]%, [Method B] achieved [Y]%, and [Method C] achieved [Z]%. The best model is [Method A]. The point forecast for the next [N] periods is [values]. Now we'll quantify the uncertainty around this forecast. Ready?"

---

### Step 3: Quantify Forecast Uncertainty

**Invoke:** `simulation/monte-carlo-simulation`

**Purpose in this workflow:** Wrap the point forecast with prediction intervals by simulating the distribution of possible future demand. Uses the historical forecast errors from Step 2 to calibrate the uncertainty.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Point forecast from Step 2 | Yes | From prior step |
| Historical forecast errors from Step 2 | Yes | From prior step |
| Confidence levels for prediction intervals | No | 80% and 95% |
| Number of simulation iterations | No | 10,000 |
| Error growth assumption (uncertainty increases with horizon) | No | Proportional to sqrt(horizon) |

**Actions:**
1. Analyze the distribution of historical forecast errors (mean, std dev, skewness, normality test)
2. Fit a probability distribution to the forecast errors:
   - Normal if errors are symmetric and pass normality test
   - Student-t if errors have heavier tails than normal
   - Empirical bootstrap if errors do not fit a standard distribution
3. For each simulation iteration and each forecast period:
   - Draw a random error from the fitted error distribution
   - Scale the error by the horizon distance (uncertainty grows with time)
   - Add the error to the point forecast to get a simulated demand realization
4. Collect the demand distribution for each forecast period
5. Calculate prediction intervals at the requested confidence levels (e.g., 80% and 95%)
6. Calculate the probability of demand exceeding specific thresholds (if relevant for capacity planning)

**Output carried forward:**
- Prediction intervals for each forecast period (lower bound, point forecast, upper bound at each confidence level)
- Demand probability distribution per period
- Probability of demand exceeding key thresholds
- These feed the optimization in Step 4

**Guided mode checkpoint:** "Uncertainty quantification complete. For period [T+1], the point forecast is [X] with 95% prediction interval of [[L], [U]]. Uncertainty grows to [[L2], [U2]] by period [T+N]. There is a [P]% probability demand exceeds [threshold]. Now we'll optimize resource allocation given this uncertainty. Ready?"

---

### Step 4: Optimize Resource Allocation

**Invoke:** `optimization/optimization-models`

**Purpose in this workflow:** Given the demand forecast and its uncertainty, optimize the resource plan (inventory, staffing, capacity) that minimizes total expected cost or maximizes service level subject to constraints.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Demand distribution from Step 3 | Yes | From prior step |
| Resource type (inventory, staffing, capacity) | Yes | Ask -- no default |
| Cost parameters | Yes | Ask -- see table below |
| Service level target | No | 95% |
| Constraints (budget, warehouse space, headcount limits) | If applicable | Unconstrained |

**Cost parameters by resource type:**

| Resource Type | Key Cost Parameters |
|---------------|-------------------|
| Inventory | Holding cost per unit per period, shortage/stockout cost per unit, ordering cost per order, lead time |
| Staffing | Regular wage per hour, overtime rate, hiring cost, firing/layoff cost, understaffing penalty |
| Capacity | Fixed capacity cost, variable production cost, overtime/expediting cost, lost sales cost |

**Actions:**
1. Formulate the optimization model:
   - **Objective:** Minimize total expected cost (holding + shortage + ordering) or maximize profit
   - **Decision variables:** Order quantities, staffing levels, or capacity allocation per period
   - **Constraints:** Budget, space, headcount, service level, lead time
2. Incorporate demand uncertainty using one of:
   - **Newsvendor model** (single period): optimal order quantity given demand distribution and cost ratio
   - **Stochastic programming** (multi-period): optimize across demand scenarios sampled from Step 3
   - **Safety stock approach**: base stock = mean demand + z * std dev, where z comes from the service level target
3. Solve the optimization using PuLP (Python) or Solver setup (Excel)
4. Report the optimal plan: quantities per period, total expected cost, service level achieved
5. Run sensitivity on key cost parameters: how does the optimal plan change if holding cost doubles? If shortage cost triples?

**Output carried forward:**
- Optimal resource plan per period (order quantities, staffing levels, or capacity)
- Total expected cost under the optimal plan
- Service level achieved
- Sensitivity analysis on cost parameters
- These feed the monitoring setup in Step 5

**Guided mode checkpoint:** "Optimization complete. The optimal [inventory/staffing/capacity] plan for the next [N] periods has total expected cost of $[X] and achieves a [Y]% service level. The plan is most sensitive to [cost parameter]. Now I'll set up the monitoring framework. Ready?"

---

### Step 5: Monitor and Update

**Purpose:** Define the framework for tracking forecast accuracy over time and triggering reforecasts when the model degrades.

**Actions:**
1. Define tracking metrics:
   - **Forecast error** per period: actual - forecast
   - **Cumulative Forecast Error (CFE)**: running sum of errors (detects bias)
   - **Tracking Signal**: CFE / MAD -- should stay between -4 and +4
   - **Rolling MAPE**: accuracy over the most recent N periods
2. Set reforecasting trigger conditions:
   - Tracking signal exceeds +/- 4 (systematic bias detected)
   - Rolling MAPE exceeds [threshold, e.g., 2x the model's training MAPE]
   - A known structural change occurs (new product launch, market entry, regulation)
   - Scheduled periodic reforecast (e.g., quarterly for monthly forecasts)
3. Define the monitoring dashboard specification:
   - Actual vs. forecast time series plot with prediction interval bands
   - Tracking signal chart with control limits
   - Rolling MAPE trend chart
   - Alert log for triggered reforecasts
4. Document the reforecast procedure: which steps to re-run (all five or just Steps 2-4)

**Output carried forward:**
- Monitoring dashboard specification
- Trigger thresholds and escalation rules
- Reforecast procedure document
- This completes the deliverable

**Guided mode checkpoint:** "Monitoring framework set. Tracking signal limits at +/- 4, rolling MAPE threshold at [X]%, scheduled reforecast every [period]. The dashboard will show actual vs. forecast with prediction bands and alert when triggers fire. The full deliverable is ready."

---

## Excel Output Specification

All formatting follows `references/excel-standards.md`. Inputs use blue font. Formulas use black font. Cross-sheet references use green font.

### Tab 1: Forecast Summary Dashboard

| Row | Metric | Source | Format |
|-----|--------|--------|--------|
| 3 | Forecast Method Selected | From Tab 3 model comparison | Text |
| 4 | Forecast Horizon | Assumptions tab | Text |
| 5 | MAPE (Best Model) | From Tab 3 | `0.0%` |
| 7 | Next Period Point Forecast | From Tab 3 | `#,##0` |
| 8 | 95% Prediction Interval | From Tab 4 | `#,##0` -- `#,##0` |
| 9 | 80% Prediction Interval | From Tab 4 | `#,##0` -- `#,##0` |
| 11 | Optimal Resource Level (next period) | From Tab 5 | `#,##0` |
| 12 | Total Expected Cost | From Tab 5 | `$#,##0` |
| 13 | Service Level Achieved | From Tab 5 | `0.0%` |
| 15 | Reforecast Trigger | From Tab 6 | Text |

### Tab 2: Data Exploration

| Section | Content | Format |
|---------|---------|--------|
| Raw data | Period, demand value | `#,##0` |
| Decomposition | Trend component, seasonal component, residual | `#,##0.0` |
| Outlier flags | Period, value, treatment | Text |
| Summary statistics | Mean, median, std dev, min, max, coefficient of variation | `#,##0` / `0.0%` |

### Tab 3: Forecast Model Comparison

| Column | Header | Format |
|--------|--------|--------|
| A | Method | Text |
| B | MAE | `#,##0.0` |
| C | MAPE | `0.0%` |
| D | RMSE | `#,##0.0` |
| E | Tracking Signal | `0.00` |
| F | Selected (Y/N) | Text |

Below the comparison table: point forecast from the selected model for each future period. Historical fit (actual vs. fitted) data for charting.

### Tab 4: Prediction Intervals

| Column | Header | Format |
|--------|--------|--------|
| A | Period | Date or index |
| B | Point Forecast | `#,##0` |
| C | 80% Lower | `#,##0` |
| D | 80% Upper | `#,##0` |
| E | 95% Lower | `#,##0` |
| F | 95% Upper | `#,##0` |
| G | P(Demand > Threshold) | `0.0%` |

Error distribution summary and simulation parameters below the table.

### Tab 5: Resource Optimization

| Column | Header | Format |
|--------|--------|--------|
| A | Period | Date or index |
| B | Expected Demand | `#,##0` |
| C | Optimal Order/Staff/Capacity | `#,##0` |
| D | Safety Stock / Buffer | `#,##0` |
| E | Holding Cost | `$#,##0` |
| F | Shortage Cost | `$#,##0` |
| G | Total Cost | `$#,##0` |

Summary below: total expected cost, service level, sensitivity table on cost parameters.

### Tab 6: Monitoring Dashboard Spec

| Section | Content | Format |
|---------|---------|--------|
| Tracking metrics | Metric name, formula, threshold, action | Text |
| Trigger conditions | Condition, threshold value, response | Text |
| Reforecast schedule | Frequency, steps to re-run | Text |
| Dashboard chart specs | Chart type, data source, layout | Text |

### Tab 7: Assumptions & Inputs

All hardcoded values. Blue font, yellow background on key assumptions.

| Cell | Label | Default | Format |
|------|-------|---------|--------|
| B3 | Forecast Horizon (periods) | (user input) | `#,##0` |
| B4 | Time Granularity | (user input) | Text |
| B5 | Simulation Iterations | 10,000 | `#,##0` |
| B6 | Service Level Target | 95% | `0.0%` |
| B7 | Holding Cost per Unit per Period | (user input) | `$#,##0.00` |
| B8 | Shortage Cost per Unit | (user input) | `$#,##0.00` |
| B9 | Ordering/Setup Cost | (user input) | `$#,##0` |
| B10 | Lead Time (periods) | (user input) | `#,##0` |
| B12+ | Additional cost parameters | (vary) | (vary) |

Named ranges: `ForecastHorizon` -> B3, `SimIterations` -> B5, `ServiceLevel` -> B6, `HoldingCost` -> B7, `ShortageCost` -> B8, `OrderingCost` -> B9, `LeadTime` -> B10.

## Python Output Specification

When Python mode is selected, produce a self-contained script using:
- `pandas` for data manipulation and time series handling
- `statsmodels` for exponential smoothing (Holt-Winters) and decomposition
- `numpy` / `scipy.stats` for error distribution fitting and Monte Carlo simulation
- `pulp` for optimization formulation and solving
- `matplotlib` for time series plots with prediction bands, model comparison bar charts, and optimization sensitivity plots

Print key results to console in a structured format. Generate charts as PNG files or display inline.

## Output

This workflow produces:

1. **A multi-tab formulated workbook** (Excel mode) or **self-contained Python script** with full analysis
2. **Data exploration** with decomposition, outlier detection, and pattern identification
3. **Forecast model comparison** with accuracy metrics and the best model selected objectively
4. **Prediction intervals** from Monte Carlo simulation that honestly quantify forecast uncertainty
5. **Optimized resource plan** (inventory, staffing, or capacity) that accounts for demand uncertainty
6. **Monitoring framework** with trigger conditions, tracking metrics, and reforecast procedures
7. **Full assumption transparency** -- change any input and watch the entire analysis update

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Reporting only a point forecast without prediction intervals | A single number gives false precision; demand is inherently uncertain | Always wrap the point forecast with Monte Carlo-derived prediction intervals (Step 3) |
| Choosing a forecasting method without comparing alternatives | The "obvious" method may not be the most accurate for this specific data | Compare at least 3 methods on held-out data and select based on MAPE/MAE (Step 2) |
| Using in-sample accuracy to select the best model | In-sample fit always favors more complex models regardless of true accuracy | Use hold-out validation: train on historical data, test on the most recent periods |
| Optimizing resources based on the point forecast alone | Ignores the cost asymmetry between overstocking and stockouts | Use the demand distribution from Step 3 as input to the optimization, not just the mean |
| Assuming forecast errors are normally distributed without checking | Errors may be skewed or heavy-tailed, leading to underestimated prediction intervals | Test the error distribution in Step 3 and use Student-t or bootstrap if normality fails |
| Setting reforecast triggers based on gut feel | Either reforecasts too often (waste) or too rarely (stale forecast) | Use tracking signal (+/- 4 limits) and rolling MAPE thresholds from Step 5 |
| Extrapolating a trend indefinitely without a saturation check | Linear trends cannot continue forever; markets saturate, capacities cap | Discuss the reasonable forecast horizon with the user and flag when extrapolation becomes speculative |
| Ignoring seasonality when it is clearly present in the data | Forecasts will systematically over- or under-predict in seasonal peaks and troughs | Use Holt-Winters or seasonal regression when decomposition reveals a seasonal pattern |
| Hardcoding cost parameters in the optimization formulas | Cannot run sensitivity analysis or update when costs change | Place all cost parameters on the Assumptions tab with blue font; reference via named ranges |

## Related Skills

- **Time Series Forecasting** (`forecasting/time-series-forecasting/`) -- Explores data patterns and builds forecast models in Steps 1-2
- **Monte Carlo Simulation** (`simulation/monte-carlo-simulation/`) -- Simulates forecast uncertainty to produce prediction intervals in Step 3
- **Optimization Models** (`optimization/optimization-models/`) -- Optimizes resource allocation given demand uncertainty in Step 4
- **Risk Assessment Pipeline** (`workflows/risk-assessment-pipeline/`) -- Sister workflow that uses Monte Carlo for risk quantification, which can incorporate demand uncertainty as a risk factor
- **Investment Analysis** (`workflows/investment-analysis/`) -- Sister workflow where demand forecasts feed revenue projections for investment decisions
