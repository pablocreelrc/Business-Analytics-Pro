---
name: Risk Assessment Pipeline
description: >
  End-to-end risk assessment workflow that chains probability modeling, Monte Carlo
  simulation, correlated inputs, and decision analysis to quantify and manage business
  risk. Produces a multi-tab workbook or Python report with risk factor inventory,
  simulation results, VaR metrics, and mitigation recommendations.
  Trigger phrases: "risk assessment pipeline", "assess my risks", "quantify business risk",
  "risk analysis workflow", "end-to-end risk assessment", "enterprise risk analysis",
  "build a risk model", "Monte Carlo risk assessment", "simulate risk factors",
  "Value at Risk analysis", "VaR and CVaR", "correlated risk simulation",
  "risk factor inventory", "probability of loss", "risk mitigation analysis",
  "operational risk quantification", "financial risk simulation", "market risk model",
  "strategic risk assessment", "risk register with simulation", "risk heat map",
  "quantitative risk analysis", "risk-adjusted decision making", "portfolio risk model",
  "downside risk analysis", "tail risk assessment", "stress test my assumptions",
  "worst-case scenario analysis", "risk management framework", "comprehensive risk report"
---

# Risk Assessment Pipeline

## Purpose

Chain four analytical skills -- probability distributions, simulation models, Monte Carlo simulation, and decision analysis -- into a single end-to-end workflow that takes a set of uncertain risk factors and produces a quantified risk profile with VaR metrics, correlation-aware simulation results, and structured mitigation recommendations.

This is a **workflow skill**, not a standalone analysis. It orchestrates four component skills in sequence, managing data handoffs between them so the user gets a complete risk assessment without manually stitching steps together.

## When to Use

- You have multiple risk factors and need to understand their combined effect on a business outcome (revenue, profit, project cost, portfolio value)
- You want to move beyond a qualitative risk register to quantitative risk modeling with probability distributions and simulation
- Risk factors are correlated (e.g., raw material cost and energy cost move together) and independence assumptions would understate total risk
- You need Value at Risk (VaR) and Conditional VaR (CVaR) metrics for reporting to leadership or regulators
- You want to identify which risk factors contribute the most to total variance so mitigation efforts can be prioritized
- There are risk mitigation decisions to evaluate (hedge, insure, diversify, accept) and you need a framework to choose optimally
- You need to present a quantified risk assessment to a board, investment committee, or risk management function

## Workflow Architecture

```
+-----------------------------------------------------------------+
|                RISK ASSESSMENT PIPELINE                           |
|                                                                  |
|  +----------------+    +-------------------+                     |
|  |  STEP 1         |    |  STEP 2            |                    |
|  |  Identify Risk  |--->|  Model             |                    |
|  |  Factors        |    |  Uncertainties      |                    |
|  +----------------+    +-------------------+                     |
|        |                       |                                  |
|        | Risk inventory,       | Parameterized                   |
|        | categories             | distributions                   |
|        |                       |                                  |
|        v                       v                                  |
|                        +-------------------+                     |
|                        |  STEP 3            |                    |
|                        |  Assess            |                    |
|                        |  Correlations      |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                | Correlation matrix,             |
|                                | dependency structure             |
|                                v                                  |
|                        +-------------------+                     |
|                        |  STEP 4            |                    |
|                        |  Simulate          |                    |
|                        |  Outcomes          |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                | Risk distribution,              |
|                                | VaR, CVaR, tornado              |
|                                v                                  |
|                        +-------------------+                     |
|                        |  STEP 5            |                    |
|                        |  Analyze and       |                    |
|                        |  Decide            |                    |
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
| 1 | `probability/probability-distributions` | Business context, known risk factors, historical data or expert estimates | Risk factor inventory with categories, preliminary distribution types | Risk factor list feeds parameterization in Step 2 |
| 2 | `probability/probability-distributions` | Risk factors from Step 1, data or expert ranges | Parameterized distributions for each risk factor (type, parameters) | Distributions feed correlation assessment in Step 3 |
| 3 | `simulation/simulation-models` | Distributions from Step 2, historical data or expert judgment on dependencies | Correlation matrix, Cholesky decomposition setup, dependency structure | Correlated input structure feeds Monte Carlo in Step 4 |
| 4 | `simulation/monte-carlo-simulation` | Correlated input model from Step 3, outcome formula linking risk factors to business metric | Risk distribution, VaR, CVaR, P(loss), tornado chart, percentile table | Risk metrics feed decision structuring in Step 5 |
| 5 | `probability/decision-analysis` | Risk metrics from Step 4, mitigation alternatives (hedge, insure, diversify, accept), costs of mitigation | Decision tree with mitigation options, EMV of each strategy, optimal risk management plan | Final deliverable |

## Process

### Entry Mode Selection

When the user invokes this workflow, determine which entry mode applies:

**Guided Mode** -- User says something like "help me assess our risks" or "walk me through a risk assessment." Run each step interactively, explaining the handoff between steps, confirming intermediate outputs before proceeding.

**Context Dump Mode** -- User provides all data (risk factors, distributions, correlations, mitigation options) upfront. Run all five steps in sequence, present the final workbook with a summary of decisions made at each stage.

**Quick Draft Mode** -- User says "just run the risk assessment" or provides risk factors inline. Use what is given, fill reasonable defaults (state them clearly), produce the complete deliverable immediately.

### Output Mode Detection

Detect the user's preferred output mode per `references/output-mode-routing.md`:

| Mode | What Happens |
|------|-------------|
| Excel | Shortcut.ai builds an IB-formatted multi-tab workbook |
| Python | Self-contained script with numpy/scipy for simulation, matplotlib for charts |
| Both | Python computes, Shortcut.ai formats results into Excel |
| Teach | Walk through the framework and math, no code output |

---

### Step 1: Identify Risk Factors

**Invoke:** `probability/probability-distributions` (concepts only)

**Purpose in this workflow:** Build a comprehensive risk factor inventory. Categorize each factor by type and assess which are most material. This step is about discovery and organization before quantification begins.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Business context (what outcome is at risk) | Yes | Ask -- no default |
| Known risk factors | Yes | Ask -- no default |
| Risk categories to consider | No | Market, Operational, Financial, Strategic |
| Historical loss or variance data | Preferred | Use expert estimates |
| Time horizon for the assessment | Yes | 1 year |

**Actions:**
1. List all uncertain variables that could affect the target business outcome
2. Categorize each risk factor: **Market** (demand, price, competition), **Operational** (supply disruption, quality, capacity), **Financial** (interest rates, FX, credit), **Strategic** (regulation, technology shift, reputation)
3. Preliminary materiality ranking -- which factors have the largest potential impact and highest likelihood
4. Identify which factors are likely correlated (flag for Step 3)
5. For each factor, note available data: historical series, expert estimates, industry benchmarks

**Output carried forward:**
- Risk factor inventory table: factor name, category, preliminary impact rating, data availability
- List of suspected correlations between factors
- Target business metric (revenue, profit, project cost, portfolio value)

**Guided mode checkpoint:** "Risk inventory complete. I've identified [N] risk factors across [categories]. The top three by preliminary impact are [X, Y, Z]. Factors [A and B] are likely correlated. Now we'll assign probability distributions to each factor. Ready to proceed?"

---

### Step 2: Model Uncertainties

**Invoke:** `probability/probability-distributions`

**Purpose in this workflow:** Select and parameterize the probability distribution for each risk factor identified in Step 1. This step transforms qualitative risk awareness into quantitative inputs for simulation.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Risk factors from Step 1 | Yes | From prior step |
| Historical data for each factor | Preferred | Use expert estimates |
| Distribution type preference | No | Triangular (min, most likely, max) |
| Expert estimates (min, most likely, max) | If no data | Ask for each factor |

**Actions:**
1. For each risk factor, select the appropriate distribution:
   - **Triangular**: when you have min, most likely, max estimates (most common for risk assessment)
   - **Normal**: when the variable is symmetric with known mean and std dev
   - **Lognormal**: for variables that cannot be negative (costs, durations, prices)
   - **Uniform**: when all values in a range are equally likely (maximum uncertainty)
   - **PERT (Beta)**: like triangular but with more weight on the most likely value
   - **Discrete**: for event risks (happens or does not happen) with assigned probability
2. Parameterize each distribution using available data or expert judgment
3. Document the rationale for each distribution choice
4. Validate by checking: does the distribution produce realistic extreme values?

**Output carried forward:**
- Distribution specifications: risk factor name, distribution type, parameters
- Rationale documentation for each choice
- These feed the correlation assessment in Step 3

**Guided mode checkpoint:** "Distributions assigned. [Factor 1] follows a triangular distribution (min=$A, mode=$B, max=$C). [Factor 2] follows a lognormal (mu=$D, sigma=$E). [Factor 3] is a discrete event with [P]% probability. Ready to assess correlations?"

---

### Step 3: Assess Correlations

**Invoke:** `simulation/simulation-models`

**Purpose in this workflow:** Define how risk factors move together. Ignoring correlations typically understates portfolio risk because bad outcomes tend to cluster. This step builds the dependency structure that makes the simulation realistic.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Distributions from Step 2 | Yes | From prior step |
| Historical data for correlation estimation | Preferred | Expert judgment |
| Known dependencies between factors | Yes | From Step 1 flags |
| Correlation coefficient estimates | If no data | Ask or use 0.3 for "moderate" |

**Actions:**
1. Build a correlation matrix for all risk factors
2. For each pair of factors, estimate the correlation coefficient:
   - From historical data (Spearman rank correlation preferred for non-normal variables)
   - From expert judgment using anchors: 0 = independent, 0.3 = weak, 0.5 = moderate, 0.7 = strong
3. Validate the correlation matrix is positive semi-definite (required for Cholesky decomposition)
4. Set up the Cholesky decomposition to generate correlated random draws in Step 4
5. Run a comparison: show the risk profile with correlations vs. without (independent case) to quantify the impact of dependency

**Output carried forward:**
- Validated correlation matrix
- Cholesky decomposition matrix (lower triangular)
- Comparison of independent vs. correlated risk profiles (preview)
- These feed the Monte Carlo simulation in Step 4

**Guided mode checkpoint:** "Correlation matrix built. [Factor A] and [Factor B] have a correlation of [r]. Accounting for correlations increases the 5th percentile loss by [X]% compared to assuming independence. Now we'll run the full Monte Carlo simulation. Ready?"

---

### Step 4: Simulate Outcomes

**Invoke:** `simulation/monte-carlo-simulation`

**Purpose in this workflow:** Run the full Monte Carlo simulation with correlated inputs to produce the risk distribution. This is where all the preparation pays off -- distributions and correlations combine to reveal the true risk profile.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Correlated input model from Step 3 | Yes | From prior step |
| Outcome formula (how risk factors map to business metric) | Yes | Ask -- no default |
| Number of iterations | No | 10,000 |
| Confidence levels for VaR/CVaR | No | 95% and 99% |

**Actions:**
1. Generate correlated random draws using Cholesky decomposition
2. For each iteration, compute the business outcome (revenue, profit, cost, etc.) using the outcome formula
3. Collect the outcome distribution (10,000 values)
4. Calculate summary statistics: mean, median, std dev, min, max, skewness, kurtosis
5. Calculate risk metrics:
   - **P(loss)**: probability the outcome falls below a threshold (e.g., P(profit < 0))
   - **VaR at 95%**: the 5th percentile loss -- "we are 95% confident the loss will not exceed $X"
   - **VaR at 99%**: the 1st percentile loss -- extreme downside
   - **CVaR (Expected Shortfall)**: average loss in the worst 5% (or 1%) of scenarios -- captures tail risk
6. Generate a tornado chart showing which risk factors contribute the most to total variance
7. Create a histogram of the outcome distribution with VaR lines marked
8. Produce percentile table (1st, 5th, 10th, 25th, 50th, 75th, 90th, 95th, 99th)

**Output carried forward:**
- Full outcome distribution with summary statistics
- VaR and CVaR at multiple confidence levels
- Tornado chart rankings -- which risk factors to mitigate first
- Percentile table for scenario planning
- These feed the decision analysis in Step 5

**Guided mode checkpoint:** "Simulation complete. The expected outcome is $X with standard deviation of $Y. There is a [Z]% probability of loss. VaR(95%) = $V -- we are 95% confident the loss will not exceed this. The risk factors contributing the most to variance are [Factor 1] and [Factor 2]. Now we'll structure risk mitigation decisions. Ready?"

---

### Step 5: Analyze and Decide

**Invoke:** `probability/decision-analysis`

**Purpose in this workflow:** Structure the risk mitigation decision. Given the quantified risk profile from Step 4, evaluate mitigation alternatives (hedge, insure, diversify, accept, avoid) and select the optimal risk management strategy.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Risk metrics from Step 4 | Yes | From prior step |
| Mitigation alternatives | Yes | Ask -- common options below |
| Cost of each mitigation | Yes | Ask -- no default |
| Risk tolerance / appetite | Yes | Ask or use VaR threshold |
| Residual risk after each mitigation | Yes | Estimate from re-simulation |

**Common mitigation alternatives:**

| Strategy | Description | Cost Structure |
|----------|-------------|---------------|
| Accept | Do nothing, retain the risk | $0 upfront, full exposure |
| Hedge | Financial instrument to offset specific risk | Premium or margin cost |
| Insure | Transfer risk to insurer | Insurance premium |
| Diversify | Spread exposure across uncorrelated sources | Operational cost of diversification |
| Avoid | Eliminate the activity that creates the risk | Foregone revenue/opportunity |
| Reduce | Operational changes to lower probability or impact | Implementation cost |

**Actions:**
1. Map the decision structure: what mitigation strategies are available, at what cost
2. For each strategy, estimate the residual risk profile (re-run simulation with mitigated inputs if needed)
3. Build a decision tree: choose mitigation strategy -> chance node (outcomes) -> payoffs
4. Calculate EMV for each mitigation strategy
5. Compare: cost of mitigation vs. reduction in expected loss
6. Calculate the "break-even" probability -- at what loss probability does each mitigation strategy become worthwhile
7. Recommend the optimal strategy and state the risk-return tradeoff clearly

**Output carried forward:**
- EMV for each mitigation strategy
- Optimal risk management recommendation
- Cost-benefit summary for each alternative
- Residual risk profile under the recommended strategy

**Guided mode checkpoint:** "Decision analysis complete. The optimal strategy is [strategy] at a cost of $X, which reduces expected loss by $Y and lowers VaR(95%) from $Z to $W. The break-even probability for this mitigation is [P]%. Here is the full comparison of all strategies."

---

## Excel Output Specification

All formatting follows `references/excel-standards.md`. Inputs use blue font. Formulas use black font. Cross-sheet references use green font.

### Tab 1: Risk Assessment Dashboard

| Row | Metric | Source | Format |
|-----|--------|--------|--------|
| 3 | Number of Risk Factors | Count from Tab 2 | `#,##0` |
| 4 | Time Horizon | Assumptions tab | Text |
| 6 | Expected Outcome | From Tab 4 simulation mean | `$#,##0` |
| 7 | Standard Deviation | From Tab 4 | `$#,##0` |
| 8 | P(Loss) | From Tab 4 | `0.0%` |
| 9 | VaR (95%) | From Tab 4 | `$#,##0` |
| 10 | VaR (99%) | From Tab 4 | `$#,##0` |
| 11 | CVaR (95%) | From Tab 4 | `$#,##0` |
| 13 | Top Risk Factor | From Tab 4 tornado | Text |
| 14 | Second Risk Factor | From Tab 4 tornado | Text |
| 16 | Recommended Mitigation | From Tab 5 | Text |
| 17 | Mitigation Cost | From Tab 5 | `$#,##0` |
| 18 | Residual VaR (95%) | From Tab 5 | `$#,##0` |

### Tab 2: Risk Factor Inventory

| Column | Header | Format |
|--------|--------|--------|
| A | Risk Factor | Text |
| B | Category | Text (Market/Operational/Financial/Strategic) |
| C | Distribution Type | Text |
| D | Parameter 1 (Min/Mean) | `#,##0.0` |
| E | Parameter 2 (Mode/Std Dev) | `#,##0.0` |
| F | Parameter 3 (Max) | `#,##0.0` |
| G | Impact Rating | Text (High/Medium/Low) |
| H | Data Source | Text |

### Tab 3: Correlation Matrix

Rows and columns = risk factor names. Cells contain correlation coefficients formatted as `0.00`. Diagonal = 1.00. Color scale: dark red for strong positive, white for zero, dark blue for strong negative.

### Tab 4: Simulation Results

| Section | Content | Format |
|---------|---------|--------|
| Summary statistics | Mean, median, std dev, min, max, skewness, kurtosis | `$#,##0` / `0.00` |
| Risk metrics | P(loss), VaR(95%), VaR(99%), CVaR(95%), CVaR(99%) | `$#,##0` / `0.0%` |
| Percentile table | 1st, 5th, 10th, 25th, 50th, 75th, 90th, 95th, 99th | `$#,##0` |
| Tornado chart data | Risk factor, low-case outcome, high-case outcome, swing | `$#,##0` |
| Histogram data | Bin edges and frequencies | `$#,##0` / `#,##0` |

### Tab 5: Mitigation Analysis

| Column | Header | Format |
|--------|--------|--------|
| A | Mitigation Strategy | Text |
| B | Cost | `$#,##0` |
| C | EMV (with mitigation) | `$#,##0` |
| D | Expected Loss Reduction | `$#,##0` |
| E | Residual VaR (95%) | `$#,##0` |
| F | Residual CVaR (95%) | `$#,##0` |
| G | Net Benefit (reduction - cost) | `$#,##0` |
| H | Recommended | Text (Yes/No) |

Decision tree structure below the summary table.

### Tab 6: Assumptions & Inputs

All hardcoded values. Blue font, yellow background on key assumptions.

| Cell | Label | Default | Format |
|------|-------|---------|--------|
| B3 | Target Business Metric | (user input) | Text |
| B4 | Time Horizon | 1 year | Text |
| B5 | Simulation Iterations | 10,000 | `#,##0` |
| B6 | VaR Confidence Level | 95% | `0.0%` |
| B7 | Loss Threshold | (user input) | `$#,##0` |
| B9+ | Risk factor parameters | (vary) | (vary) |

Named ranges: `TimeHorizon` -> B4, `SimIterations` -> B5, `VaRConfidence` -> B6, `LossThreshold` -> B7.

## Python Output Specification

When Python mode is selected, produce a self-contained script using:
- `numpy` for random sampling and outcome calculations
- `scipy.stats` for probability distributions (triangular, normal, lognormal, uniform, PERT)
- `numpy.linalg.cholesky` for correlated input generation
- `matplotlib` for histograms with VaR lines, tornado charts, and correlation heatmaps

Print key results to console in a structured format. Generate charts as PNG files or display inline.

## Output

This workflow produces:

1. **A multi-tab formulated workbook** (Excel mode) or **self-contained Python script** with full analysis
2. **Risk factor inventory** categorized by type with parameterized distributions
3. **Correlation-aware simulation** that correctly captures how risks compound when they move together
4. **VaR and CVaR metrics** at multiple confidence levels for risk reporting
5. **Tornado chart** identifying which risk factors contribute the most to total variance
6. **Mitigation recommendation** with cost-benefit analysis comparing hedge, insure, diversify, accept, and other strategies
7. **Full assumption transparency** -- change any input and watch the entire analysis update

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Listing risks qualitatively without quantifying them | "High/Medium/Low" heat maps give no decision-useful information about dollar impact or probability | Assign probability distributions and simulate to get actual dollar-denominated risk metrics |
| Assuming risk factors are independent when they clearly move together | Understates tail risk; the worst scenarios happen when multiple risks hit simultaneously | Build a correlation matrix in Step 3 and use Cholesky decomposition for correlated draws |
| Using VaR alone without CVaR | VaR tells you the threshold but nothing about how bad it gets beyond that threshold | Always report CVaR alongside VaR to capture tail severity |
| Running Monte Carlo without validating distributions first | Wrong distributions produce wrong risk metrics regardless of iteration count | Validate each distribution against historical data or expert judgment in Step 2 |
| Treating all risks as equally important | Dilutes mitigation focus across too many factors | Use the tornado chart from Step 4 to prioritize the top 2-3 risk factors for mitigation |
| Mitigating risks without comparing cost to benefit | Mitigation that costs more than the expected loss reduction destroys value | Calculate net benefit (expected loss reduction minus mitigation cost) in Step 5 |
| Using too few simulation iterations for tail risk metrics | VaR(99%) from 1,000 iterations is based on only 10 observations -- unreliable | Use at least 10,000 iterations; for 99th percentile metrics, consider 50,000+ |
| Ignoring correlation between risk factors and mitigation effectiveness | Hedging one risk may increase exposure to a correlated risk | Re-simulate with mitigation in place to capture the full residual risk profile |
| Presenting risk metrics without actionable mitigation recommendations | Analysis without decision support is academic exercise, not risk management | Always conclude with Step 5: structured decision on how to respond to the quantified risks |

## Related Skills

- **Probability Distributions** (`probability/probability-distributions/`) -- Selects and parameterizes distributions for risk factors in Steps 1-2
- **Simulation Models** (`simulation/simulation-models/`) -- Builds the correlated input structure with Cholesky decomposition in Step 3
- **Monte Carlo Simulation** (`simulation/monte-carlo-simulation/`) -- Runs the full simulation to produce risk distributions in Step 4
- **Decision Analysis** (`probability/decision-analysis/`) -- Structures the mitigation decision and calculates EMV for each strategy in Step 5
- **Investment Analysis** (`workflows/investment-analysis/`) -- Sister workflow that uses risk metrics to evaluate and allocate across investment alternatives
- **Demand Forecasting Pipeline** (`workflows/demand-forecasting-pipeline/`) -- Sister workflow that uses Monte Carlo for forecast uncertainty, which can feed into risk assessment
