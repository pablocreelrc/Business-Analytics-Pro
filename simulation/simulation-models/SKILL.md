---
name: Simulation Models
description: >
  Build multi-variable simulation models with correlated inputs. Use this skill when you need to
  simulate bidding strategy, warranty cost estimation, production with uncertain yield, cash balance
  forecasting, customer loyalty modeling, financial planning under uncertainty, investment simulation,
  sales forecasting with dependencies, marketing campaign simulation, correlated risk analysis,
  Cholesky decomposition for random variables, copula-based dependency modeling, operations simulation,
  competitive bidding optimization, repair cost modeling, multi-input Monte Carlo, rank correlation,
  Spearman correlation in simulation, portfolio simulation with correlated returns, demand-supply
  uncertainty modeling, or any scenario where inputs move together and independence is wrong.
---

# Simulation Models

## Purpose

Move beyond single-variable Monte Carlo into realistic multi-variable simulations where inputs are correlated. Covers operations (bidding, warranty, production yield), finance (cash balance, investment, planning), and marketing (loyalty, sales) -- all with proper dependency modeling so risk is not underestimated.

## When to Use

- Problem has **two or more uncertain inputs** that are not independent
- You need to model **bidding** against competitors with uncertain costs
- **Warranty or repair costs** depend on failure probabilities over time
- **Cash balance** fluctuates with random inflows and outflows
- **Production yield** is uncertain and correlated with input quality
- Customer **loyalty or retention** depends on multiple correlated drivers
- You want to **compare results** with vs. without correlation to show why it matters
- An upstream `monte-carlo-simulation` or `probability-distributions` skill has been completed and you need the next level of realism

## Foundation

### Why Correlation Matters

Assuming independence when inputs are correlated **underestimates tail risk**. If material cost and labor cost rise together (positive correlation), the probability of extreme total cost is higher than independent simulation suggests. Ignoring this produces overconfident decisions.

### Correlation Coefficient

```
rho = Cov(X, Y) / (sigma_X * sigma_Y)
```

- rho = +1: perfect positive linear relationship
- rho = 0: no linear relationship (may still be nonlinearly dependent)
- rho = -1: perfect negative linear relationship

### Rank Correlation (Spearman)

For non-linear dependencies, use Spearman rank correlation -- measures monotonic association without assuming linearity. Preferred when distributions are non-normal or relationships are curved.

### Cholesky Decomposition

To generate correlated random variables from a correlation matrix **R**:

1. Decompose: R = L * L^T where L is lower-triangular (Cholesky factor)
2. Generate independent standard normals: Z = [Z1, Z2, ..., Zn]
3. Correlated normals: X = L * Z
4. Transform to target marginal distributions via inverse CDF

This preserves the specified correlation structure while allowing any marginal distribution.

### Copulas (Advanced)

When marginals are non-normal and you need precise tail dependency:
- **Gaussian copula**: correlate via multivariate normal, then transform margins
- **t-copula**: heavier tails, better for financial risk
- Copulas separate dependency structure from marginal distributions

### Key Model Types

**Bidding Models**
- Cost ~ Distribution(params) with uncertainty
- Competitors' bids modeled as random variables
- Optimal bid = f(estimated cost, markup, number of competitors, cost uncertainty)
- Win probability decreases as bid increases; expected profit = P(win) * (bid - cost)

**Warranty Cost Models**
```
E(cost) = SUM over t [ P(failure at t) * repair_cost_t ]
```
- Failure probabilities may follow Weibull or exponential distributions
- Repair costs may escalate over time
- Simulate total warranty exposure across a portfolio of products

**Cash Balance Simulation**
- Starting balance + random inflows - random outflows each period
- Inflows and outflows may be correlated (e.g., both depend on economic conditions)
- Track probability of negative balance (liquidity risk)

**Production with Uncertain Yield**
- Input quantity * yield rate = output, where yield is uncertain
- Yield may correlate with input quality or batch size
- Simulate to find optimal input quantity given yield uncertainty

## Process

### Step 1: Detect Output Mode

Scan the user message for mode signals:

| Priority | Signal | Mode |
|----------|--------|------|
| 1 | "both", "Excel and Python" | Both |
| 2 | "Excel", "spreadsheet", "workbook" | Excel |
| 2 | "Python", "script", "simulate", "run" | Python |
| 3 | "explain", "teach", "how does", "walk me through" | Teach |
| 4 | No signal detected | Ask user |

### Step 2: Detect Entry Mode

**Guided Mode** -- User says "help me build" or asks questions:
1. Ask: What type of simulation? (bidding / warranty / cash balance / production / financial / marketing / custom)
2. Ask: What are the uncertain inputs and their distributions?
3. Ask: Are any inputs correlated? Do you have a correlation matrix or estimates?
4. Ask: How many iterations? (default: 10,000)
5. Ask: What decisions or outputs matter? (optimal bid, expected cost, probability of shortfall, etc.)
6. Confirm parameters, then build.

**Context Dump Mode** -- User pastes a problem description or case:
1. Parse the problem for: model type, input distributions, correlations, decision variables
2. State what you extracted and any assumptions
3. Ask for confirmation or corrections
4. Build immediately after confirmation

**Quick Draft Mode** -- User says "just build it" or provides minimal parameters:
1. Use stated parameters plus sensible defaults
2. Default: 10,000 iterations, uniform correlation of 0.3 if not specified
3. Build immediately, note all assumptions in output

### Step 3: Build the Model

For all modes, the core simulation logic:

1. **Define inputs**: distributions, parameters, correlation matrix
2. **Generate correlated random variables** via Cholesky decomposition
3. **Run simulation**: apply business logic to each iteration
4. **Compute outputs**: summary statistics, percentiles, decision metrics
5. **Compare**: run the same model WITHOUT correlation, show the difference
6. **Present results** in the detected output mode

## Excel Output Specification

**Tool**: Shortcut.ai API (`shortcut_excel.py`) -- never openpyxl or xlsxwriter.

**Formatting**: IB standard per `references/excel-standards.md`.

### Tab Structure

**Tab 1: Assumptions**
- All input distributions with parameters (blue font, yellow fill)
- Correlation matrix (blue font, yellow fill for user-editable cells)
- Number of iterations
- Named ranges for all key inputs

**Tab 2: Correlation Matrix**
- Full correlation matrix with variable labels
- Cholesky factor (L matrix) displayed below
- Format: `0.000` for all correlation values
- Diagonal = 1.000, symmetric structure

**Tab 3: Simulation Results**
- Iteration number in Column A
- Each input variable in subsequent columns
- Output variable(s) in final columns
- First 100 rows visible; full data available
- Format: per `references/analytics-format-codes.md`

**Tab 4: Comparison**
- Side-by-side: With Correlation vs. Without Correlation
- Metrics: Mean, Std Dev, P5, P25, P50, P75, P95, Min, Max
- Difference column showing impact of correlation
- Key insight row: "Correlation increases tail risk by X%"

**Tab 5: Summary Statistics**
- Decision-relevant metrics (optimal bid, expected cost, probability thresholds)
- Tornado-style sensitivity if applicable
- Format: headers bold white on navy, data right-aligned

### Number Formats

| Metric | Format |
|--------|--------|
| Correlation coefficients | `0.000` |
| Simulation outputs (costs, revenue) | `#,##0;(#,##0)` |
| Probabilities | `0.0%` |
| Percentiles | `#,##0` |
| Standard deviation | `#,##0.00` |

## Python Output Specification

**Libraries**: numpy, scipy.stats, matplotlib, pandas

### Script Structure

```python
# --- Configuration ---
# Input distributions, correlation matrix, iterations

# --- Correlated Variable Generation ---
# Cholesky decomposition + inverse CDF transform

# --- Simulation Engine ---
# Business logic applied to each iteration

# --- Without-Correlation Benchmark ---
# Same model but independent inputs

# --- Analysis ---
# Summary statistics, percentiles, comparison

# --- Visualization ---
# 1. Scatter plot matrix of correlated inputs
# 2. Side-by-side histograms (with vs. without correlation)
# 3. CDF comparison overlay
# 4. Output distribution with percentile markers

# --- Console Output ---
# Comparison table printed to terminal
```

### Visualization Requirements

1. **Scatter plots**: show input-input correlation visually (2x2 or NxN grid)
2. **Histograms**: side-by-side comparing with-correlation vs. without-correlation output
3. **CDF overlay**: cumulative distribution comparison
4. **Annotated percentiles**: P5, P50, P95 marked on output distribution
5. Use `plt.style.use('seaborn-v0_8')` or similar clean style
6. Label axes, title every chart, include legend

## Output

Regardless of mode, always deliver:

1. **Stated assumptions**: every distribution, parameter, and correlation value
2. **Correlation impact**: quantified difference between correlated and independent simulation
3. **Decision metric**: the specific number the user needs (optimal bid, expected warranty cost, probability of cash shortfall, etc.)
4. **Risk insight**: one sentence on how correlation changes the risk profile
5. **Sensitivity note**: which correlation assumption matters most

For **Teach mode**: explain all of the above with formulas in code blocks, worked examples, and intuition -- no files created.

## Anti-Patterns

1. **Ignoring correlation entirely** -- The most common and most dangerous mistake. If inputs move together in reality, independent simulation produces misleading risk estimates.

2. **Assuming independence by default** -- "I don't know the correlation, so I'll assume zero." A correlation of 0.3 is almost always more realistic than 0.0 for business inputs.

3. **Using Pearson correlation for non-linear dependencies** -- Pearson measures linear association only. Use Spearman rank correlation when relationships are monotonic but nonlinear.

4. **Applying Cholesky to a non-positive-definite matrix** -- If the user provides an inconsistent correlation matrix, Cholesky will fail. Always check positive definiteness first and suggest nearest valid matrix if needed.

5. **Too few iterations for tail analysis** -- 1,000 iterations may be fine for means but inadequate for P1 or P99 estimates. Use at least 10,000; for tail risk use 50,000+.

6. **Confusing correlation with causation in the model** -- Correlation structures capture co-movement, not causal direction. Document this clearly so users don't misinterpret.

7. **Hardcoding correlation in the simulation loop** -- Correlation should be applied at the variable generation step via Cholesky, not hacked into individual draws. Doing it wrong breaks the joint distribution.

8. **Skipping the with-vs-without comparison** -- The whole point of correlated simulation is to show the difference. Always run both and present the delta.

9. **Using normal distributions for everything** -- Many business variables (costs, times, demand) are skewed. Use lognormal, triangular, beta, or Weibull where appropriate, then apply Cholesky + inverse CDF.

10. **Not validating generated correlations** -- After generating correlated samples, compute the realized correlation matrix and compare to the target. Large deviations indicate a bug.

## Related Skills

- **Requires**: `monte-carlo-simulation` (single-variable simulation foundations), `probability-distributions` (fitting and selecting input distributions)
- **Feeds into**: `investment-analysis` workflow (portfolio simulation with correlated returns), `project-valuation` workflow (multi-risk NPV simulation)
- **Complements**: `decision-analysis` (use simulation outputs as inputs to decision trees), `optimization-models` (optimize decisions under simulated uncertainty)
