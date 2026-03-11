---
name: Project Valuation
description: >
  Values a project with embedded managerial flexibility using real options analysis.
  Chains spreadsheet modeling, probability distributions, Monte Carlo simulation, and
  decision analysis into a single workflow. Compares static NPV with flexibility-adjusted
  NPV to quantify the value of real options.
  Trigger phrases: "project valuation", "value this project", "real options analysis",
  "real options valuation", "value of flexibility", "option to expand",
  "option to abandon", "option to defer", "managerial flexibility",
  "strategic options valuation", "NPV with flexibility", "NPV without flexibility",
  "expanded NPV", "static NPV vs flexible NPV", "value of waiting",
  "defer or invest now", "abandonment option value", "expansion option value",
  "staging investment", "phased investment analysis", "project with embedded options",
  "flexibility premium", "what is the option worth", "should we pilot first",
  "value of the pilot", "option to scale", "growth option", "contraction option",
  "switching option", "real option decision tree", "binomial real option",
  "simulate then decide", "value of managerial discretion",
  "compare rigid vs flexible investment"
---

# Project Valuation Workflow

## Purpose

Value a project that has embedded managerial flexibility -- the ability to expand, abandon, defer, or switch course based on how uncertainty resolves -- by comparing static NPV (no flexibility) with flexibility-adjusted NPV (with real options), quantifying the incremental value of that flexibility.

This is a **workflow skill**, not a standalone analysis. It orchestrates four component skills in sequence: build the base case, model uncertainty, simulate without flexibility, then value the flexibility through decision tree analysis.

## When to Use

- You are evaluating a project where management can make contingent decisions as uncertainty resolves (expand if demand is high, abandon if costs overrun, defer until market conditions improve)
- You suspect the traditional NPV understates the project's value because it ignores embedded options
- You need to quantify the dollar value of staging an investment (pilot then full rollout) versus committing all capital upfront
- You want to compare a rigid investment plan with a flexible one to justify the cost of maintaining optionality
- You need to convince an investment committee that a negative-NPV project may still be worth pursuing because of its embedded options
- Leadership asks "what is the option to expand worth?" or "should we wait?"
- You are evaluating R&D, phased real estate development, natural resource extraction, or any investment with significant uncertainty and managerial discretion

## Workflow Architecture

```
+-----------------------------------------------------------------+
|                  PROJECT VALUATION WORKFLOW                       |
|                                                                  |
|  +----------------+    +-------------------+                     |
|  |  STEP 1         |    |  STEP 2            |                    |
|  |  Define the     |--->|  Model             |                    |
|  |  Project        |    |  Uncertainty        |                    |
|  +----------------+    +-------------------+                     |
|        |                       |                                  |
|        | Base case cash        | Parameterized                   |
|        | flows, static NPV     | distributions                   |
|        |                       |                                  |
|        v                       v                                  |
|  +-------------------------------------------+                   |
|  |  STEP 3                                     |                   |
|  |  Simulate Base Case (No Flexibility)        |                   |
|  |  NPV distribution assuming rigid plan       |                   |
|  +-------------------------------------------+                   |
|                       |                                           |
|                       | Static NPV distribution                  |
|                       v                                           |
|  +-------------------------------------------+                   |
|  |  STEP 4                                     |                   |
|  |  Identify and Value Flexibility             |                   |
|  |  Decision tree with embedded options        |                   |
|  +-------------------------------------------+                   |
|                       |                                           |
|                       v                                           |
|  +-------------------------------------------+                   |
|  |  STEP 5                                     |                   |
|  |  Compare: Static vs. Flexible               |                   |
|  |  Value of Real Options =                    |                   |
|  |    NPV(with flex) - NPV(without flex)       |                   |
|  +-------------------------------------------+                   |
|                       |                                           |
|                       v                                           |
|               +---------------+                                   |
|               |  DELIVERABLE   |                                   |
|               +---------------+                                   |
+-----------------------------------------------------------------+
```

### Step-Level Inputs and Outputs

| Step | Skill | Inputs | Outputs | Handoff to Next |
|------|-------|--------|---------|-----------------|
| 1 | `foundations/spreadsheet-modeling` | Project cash flows, investment cost, discount rate, time horizon | Base case NPV, IRR, cash flow model | Model structure feeds distribution assignment in Step 2 |
| 2 | `probability/probability-distributions` | Uncertain variables from Step 1, historical data or expert estimates | Parameterized distributions (type, parameters) for each uncertain input | Distributions feed Monte Carlo in Step 3 |
| 3 | `simulation/monte-carlo-simulation` | Cash flow model from Step 1, distributions from Step 2 | NPV distribution without flexibility, P(NPV<0), VaR, risk metrics | Static NPV distribution serves as baseline for comparison in Step 5 |
| 4 | `probability/decision-analysis` | Decision points (expand, abandon, defer), trigger conditions, option costs/payoffs, probabilities from Step 3 | Decision tree with option values, EMV with flexibility, optimal contingent strategy | Flexible NPV feeds comparison in Step 5 |
| 5 | Comparison | Static NPV (Step 3) vs. Flexible NPV (Step 4) | Value of real options = difference | Final deliverable |

## Process

### Entry Mode Selection

**Guided Mode** -- User says "help me value this project" or "walk me through real options." Run each step interactively, confirming the base case before modeling uncertainty, and confirming the static NPV before introducing flexibility.

**Context Dump Mode** -- User provides project details, cash flows, uncertainties, and flexibility options upfront. Run all five steps in sequence, present the final deliverable with assumptions stated.

**Quick Draft Mode** -- User says "just value the project" or provides numbers inline. Use what is given, fill reasonable defaults, produce the deliverable immediately.

### Output Mode Detection

Detect the user's preferred output mode per `references/output-mode-routing.md`:

| Mode | What Happens |
|------|-------------|
| Excel | Shortcut.ai builds an IB-formatted multi-tab workbook |
| Python | Self-contained script with numpy/scipy for simulation, matplotlib for charts |
| Both | Python computes, Shortcut.ai formats results into Excel |
| Teach | Walk through the framework and math, no code output |

---

### Step 1: Define the Project

**Invoke:** `foundations/spreadsheet-modeling`

**Purpose in this workflow:** Establish the deterministic base case. This is the "rigid plan" NPV -- what the project is worth if management commits to a fixed plan with no ability to adjust.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Project description | Yes | Ask -- no default |
| Initial investment cost | Yes | Ask -- no default |
| Projected cash flows per period | Yes | Ask -- no default |
| Time horizon (years/periods) | Yes | 5 years |
| Discount rate (WACC or risk-adjusted) | Yes | 10% |
| Salvage/terminal value | If applicable | Zero |

**Actions:**
1. Build the cash flow projection period by period
2. Calculate static NPV (discounted cash flows minus initial investment)
3. Calculate IRR and payback period
4. Run one-way sensitivity on key assumptions to identify which inputs drive the most value
5. Flag the most uncertain inputs -- these will get probability distributions in Step 2

**Output carried forward:**
- Cash flow model with clearly identified uncertain cells
- Static NPV, IRR, payback
- Sensitivity rankings of uncertain inputs

**Guided mode checkpoint:** "Base case NPV is $X with IRR of Y%. The model is most sensitive to [variable 1] and [variable 2]. These will be the key uncertainties we simulate. Ready to assign probability distributions?"

---

### Step 2: Model Uncertainty

**Invoke:** `probability/probability-distributions`

**Purpose in this workflow:** Select and parameterize the probability distributions that will drive the Monte Carlo simulation. This step bridges the deterministic model and the stochastic simulation.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Uncertain variables (from Step 1) | Yes | Top 2-3 from sensitivity |
| Historical data for each variable | Preferred | Use expert estimates |
| Distribution type preference | No | Triangular (min, most likely, max) |
| Correlation structure | If applicable | Independent |

**Actions:**
1. For each uncertain variable, select the appropriate distribution:
   - **Triangular**: when you have min, most likely, max estimates
   - **Normal**: when the variable is symmetric with known mean and std dev
   - **Lognormal**: for variables that cannot be negative (prices, demand)
   - **Uniform**: when all values in a range are equally likely
   - **Custom/empirical**: when historical data is available
2. Parameterize each distribution (mean, std dev, min, max, shape, etc.)
3. Document the rationale for each distribution choice
4. Specify correlations between variables if they move together

**Output carried forward:**
- Distribution specifications: variable name, distribution type, parameters
- Correlation matrix (if applicable)
- These feed directly into the Monte Carlo engine in Step 3

**Guided mode checkpoint:** "Distributions assigned: [Variable 1] follows a triangular distribution (min=$A, mode=$B, max=$C), [Variable 2] follows a lognormal (mu=$D, sigma=$E). Ready to simulate?"

---

### Step 3: Simulate Base Case (No Flexibility)

**Invoke:** `simulation/monte-carlo-simulation`

**Purpose in this workflow:** Generate the NPV distribution under the rigid plan -- the value of the project if management cannot adjust course regardless of how uncertainty resolves. This is the benchmark against which flexibility will be measured.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Cash flow model from Step 1 | Yes | From prior step |
| Distributions from Step 2 | Yes | From prior step |
| Number of iterations | No | 10,000 |
| Random seed (for reproducibility) | No | None |

**Actions:**
1. Run Monte Carlo simulation: for each iteration, draw random values from the specified distributions, compute cash flows, compute NPV
2. Collect the NPV distribution (10,000 values)
3. Calculate summary statistics: mean, median, std dev, min, max
4. Calculate risk metrics: P(NPV < 0), VaR at 5th percentile, CVaR at 5th percentile
5. Generate a histogram of the NPV distribution
6. Create a tornado chart showing which variables contribute most to NPV variance

**Output carried forward:**
- NPV distribution statistics (mean, std dev, percentiles)
- P(NPV < 0) -- probability the project destroys value under the rigid plan
- This is the **"without flexibility" baseline** for comparison in Step 5

**Guided mode checkpoint:** "Without flexibility, the project has mean NPV of $X, with [Y]% probability of negative NPV. The 5th percentile (worst realistic case) is $Z. Now we'll value the flexibility to [expand/abandon/defer]. Ready?"

---

### Step 4: Identify and Value Flexibility

**Invoke:** `probability/decision-analysis`

**Purpose in this workflow:** Map the real options embedded in the project and build a decision tree that values them. The key insight: management does not have to follow the rigid plan. They can expand if things go well, abandon if they go badly, or defer until uncertainty resolves.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Types of flexibility available | Yes | Ask user to identify |
| Option to expand: expansion cost, additional cash flows | If applicable | Ask |
| Option to abandon: salvage value, timing | If applicable | Ask |
| Option to defer: deferral period, cost of waiting | If applicable | Ask |
| Trigger conditions (what outcome triggers each option) | Yes | Based on Step 3 percentiles |
| Probabilities for favorable/unfavorable outcomes | Yes | From Step 3 distribution |

**Common Real Options:**

| Option Type | Description | When It Applies |
|-------------|-------------|----------------|
| Expand | Invest additional capital to scale up if demand is high | Growth projects, new markets |
| Abandon | Stop the project and recover salvage value if outcomes are poor | Capital-intensive projects |
| Defer | Wait to invest until uncertainty resolves | Projects where timing is flexible |
| Contract | Scale down operations if demand is lower than expected | Projects with variable cost structures |
| Switch | Change inputs, outputs, or technology | Multi-use assets |
| Stage | Break investment into phases, with a go/no-go decision at each phase | R&D, phased development |

**Actions:**
1. Map the decision structure: what options exist, when they can be exercised, what triggers them
2. Build a decision tree with decision nodes (squares) and chance nodes (circles)
3. Assign probabilities from the Step 3 simulation (e.g., P(high demand) = fraction of simulations where demand > threshold)
4. Assign payoffs at terminal nodes incorporating the option exercise
5. Solve the tree using backward induction to get EMV with flexibility
6. Calculate the value of each option individually and in combination

**Output carried forward:**
- EMV with flexibility (the "flexible NPV")
- Value of each real option = EMV(with that option) - EMV(without)
- Optimal contingent strategy: what to do under each scenario

**Guided mode checkpoint:** "With the option to [expand/abandon/defer], the project EMV increases from $X (static) to $Y (flexible). The [option type] alone is worth $Z. The optimal strategy is: [proceed/wait], and if [favorable outcome], [expand/continue]; if [unfavorable outcome], [abandon/contract]."

---

### Step 5: Compare Static vs. Flexible

**Purpose:** The payoff of the entire workflow -- quantify the value of managerial flexibility.

**Key calculation:**
```
Value of Real Options = NPV(with flexibility) - NPV(without flexibility)
```

**Actions:**
1. Present the comparison table: static NPV vs. flexible NPV vs. value of options
2. Break down the value by option type (if multiple options exist)
3. Show how the recommendation changes: a project with negative static NPV may have positive flexible NPV
4. Sensitivity analysis: how does the option value change with volatility, time to expiration, and exercise cost?
5. Assemble the final deliverable

---

## Excel Output Specification

All formatting follows `references/excel-standards.md`. Inputs use blue font. Formulas use black font. Cross-sheet references use green font.

### Tab 1: Valuation Summary

| Row | Metric | Source | Format |
|-----|--------|--------|--------|
| 3 | Static NPV (No Flexibility) | From Tab 3 simulation mean | `$#,##0` |
| 4 | Flexible NPV (With Options) | From Tab 4 decision tree EMV | `$#,##0` |
| 5 | **Value of Real Options** | `=B4-B3` | `$#,##0` (bold, double border) |
| 7 | P(NPV < 0) -- Static | From Tab 3 | `0.0%` |
| 8 | P(NPV < 0) -- Flexible | From Tab 4 | `0.0%` |
| 10 | Optimal Strategy | From Tab 4 | Text |
| 12 | Option Breakdown | | |
| 13 | Option to [Expand] | From Tab 4 | `$#,##0` |
| 14 | Option to [Abandon] | From Tab 4 | `$#,##0` |

### Tab 2: Base Case Cash Flows

Period-by-period cash flow model. Rows = periods, columns = revenue, costs, net cash flow, discount factor, PV, cumulative PV. Static NPV and IRR at bottom.

### Tab 3: Simulation Results (No Flexibility)

| Section | Content | Format |
|---------|---------|--------|
| Summary statistics | Mean, median, std dev, min, max, P(NPV<0), VaR, CVaR | `$#,##0` / `0.0%` |
| Histogram data | Bin edges and frequencies for NPV distribution | `$#,##0` / `#,##0` |
| Tornado chart data | Variable name, low-case NPV, high-case NPV, swing | `$#,##0` |

### Tab 4: Decision Tree & Real Options

| Column | Header | Format |
|--------|--------|--------|
| A | Node ID | `#,##0` |
| B | Node Type | Text (Decision/Chance/Terminal) |
| C | Parent Node | `#,##0` |
| D | Branch Label | Text |
| E | Probability | `0.0%` |
| F | Payoff (Terminal) | `$#,##0` |
| G | EMV | `$#,##0` |
| H | Option Value | `$#,##0` |

Below the tree: option value calculations and optimal strategy description.

### Tab 5: Comparison & Sensitivity

| Column | Header | Format |
|--------|--------|--------|
| A | Scenario | Text |
| B | Static NPV | `$#,##0` |
| C | Flexible NPV | `$#,##0` |
| D | Option Value | `$#,##0` |
| E | Recommendation | Text |

Sensitivity table below: how option value changes with volatility (rows) and time to expiration (columns).

### Tab 6: Assumptions & Inputs

All hardcoded values. Blue font, yellow background on key assumptions.

| Cell | Label | Default | Format |
|------|-------|---------|--------|
| B3 | Discount Rate | 10% | `0.0%` |
| B4 | Time Horizon (years) | 5 | `#,##0` |
| B5 | Initial Investment | (user input) | `$#,##0` |
| B6 | Simulation Iterations | 10,000 | `#,##0` |
| B8 | Expansion Cost | (user input) | `$#,##0` |
| B9 | Expansion Revenue Multiplier | 1.5x | `0.0x` |
| B10 | Salvage Value (Abandonment) | (user input) | `$#,##0` |
| B11 | Deferral Period (years) | 1 | `#,##0` |
| B13+ | Distribution Parameters | (vary) | (vary) |

Named ranges: `DiscountRate` -> B3, `TimeHorizon` -> B4, `InitialInvestment` -> B5, `SimIterations` -> B6, `ExpansionCost` -> B8, `SalvageValue` -> B10.

## Python Output Specification

When Python mode is selected, produce a self-contained script using:
- `numpy` for cash flow calculations and random sampling
- `scipy.stats` for probability distributions (triangular, lognormal, normal)
- `matplotlib` for NPV histograms (static vs. flexible overlay), tornado charts, and option value sensitivity heatmaps

Print the key comparison table to console. Display charts inline or save as PNG.

## Output

This workflow produces:

1. **A multi-tab formulated workbook** (Excel mode) or **self-contained Python script** with full analysis
2. **Static NPV distribution** -- what the project is worth under a rigid plan with no managerial flexibility
3. **Flexible NPV with real options** -- what the project is worth when management can expand, abandon, defer, or switch
4. **Value of real options** -- the dollar difference, quantifying exactly how much flexibility is worth
5. **Optimal contingent strategy** -- what to do under each scenario (expand if X, abandon if Y, defer until Z)
6. **Sensitivity analysis** -- how option value changes with key parameters (volatility, time, exercise cost)

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Reporting only static NPV without considering flexibility | Systematically undervalues projects with embedded options; leads to rejecting good investments | Always check whether managerial flexibility exists, and if so, value it |
| Using Black-Scholes for real options without justification | Black-Scholes assumes continuous trading, no dividends, lognormal prices -- rarely appropriate for real assets | Use decision trees with discrete scenarios informed by Monte Carlo; use Black-Scholes only as a sanity check |
| Valuing options without first building a solid base case | Option value is meaningless if the underlying cash flow model is wrong | Always start with Step 1 (deterministic model) and validate the base case before introducing options |
| Assigning arbitrary probabilities to decision tree branches | Garbage in, garbage out -- the option value is only as good as the probabilities | Derive probabilities from the Monte Carlo simulation in Step 3 |
| Double-counting flexibility in the discount rate and the option value | If you already lowered the discount rate to account for flexibility, adding option value on top double-counts | Use the same discount rate for static and flexible NPV; let the decision tree capture the flexibility value |
| Ignoring the cost of maintaining flexibility | Options are not free -- staging an investment may cost more than committing upfront | Include the cost of optionality (e.g., higher per-unit cost of phased construction) in the decision tree payoffs |
| Treating all uncertainty as risk that adds option value | Only uncertainty that management can respond to creates option value; pure background risk does not | Distinguish between actionable uncertainty (triggers a decision) and background uncertainty (cannot be acted on) |
| Comparing flexible NPV to zero instead of to static NPV | The question is not "is the flexible project worth doing?" but "how much is the flexibility worth?" | Always compute Value of Options = Flexible NPV - Static NPV as the key metric |

## Related Skills

- **Spreadsheet Modeling** (`foundations/spreadsheet-modeling/`) -- Builds the deterministic cash flow model in Step 1
- **Probability Distributions** (`probability/probability-distributions/`) -- Selects and parameterizes distributions for uncertain inputs in Step 2
- **Monte Carlo Simulation** (`simulation/monte-carlo-simulation/`) -- Simulates the NPV distribution without flexibility in Step 3
- **Decision Analysis** (`probability/decision-analysis/`) -- Values the real options through decision tree analysis in Step 4
- **Investment Analysis** (`workflows/investment-analysis/`) -- Sister workflow focused on comparing and optimizing across multiple investment alternatives
