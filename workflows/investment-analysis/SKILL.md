---
name: Investment Analysis
description: >
  End-to-end investment analysis workflow that chains spreadsheet modeling, Monte Carlo
  simulation, decision analysis, and optimization into a single process. Produces a
  multi-tab workbook or Python report with risk-adjusted recommendations.
  Trigger phrases: "investment analysis workflow", "analyze this investment",
  "should I invest", "investment decision", "evaluate investment alternatives",
  "NPV and IRR analysis", "risk-adjusted investment", "compare investments",
  "capital allocation", "investment portfolio optimization", "investment under uncertainty",
  "Monte Carlo investment", "simulate investment outcomes", "investment decision tree",
  "real options analysis", "optimal capital allocation", "budget allocation across projects",
  "investment risk analysis", "which project should we fund", "rank investments",
  "investment feasibility study", "go no-go decision", "project selection",
  "multi-criteria investment", "expected monetary value", "EVPI for investment",
  "full investment pipeline", "investment recommendation", "investment memo model",
  "simulate NPV distribution", "portfolio optimization under uncertainty"
---

# Investment Analysis Workflow

## Purpose

Chain four analytical skills -- spreadsheet modeling, Monte Carlo simulation, decision analysis, and optimization -- into a single end-to-end workflow that takes investment alternatives with uncertain cash flows and produces a risk-adjusted recommendation with optimal capital allocation.

This is a **workflow skill**, not a standalone analysis. It orchestrates four component skills in sequence, managing the data handoffs between them so the user gets a complete investment recommendation without manually stitching steps together.

## When to Use

- You have one or more investment alternatives and need a rigorous, quantitative recommendation on which to pursue
- You want to move beyond single-point NPV/IRR estimates to understand the full distribution of possible outcomes
- There are sequential decision points (e.g., pilot then expand, invest now or defer) that create embedded optionality
- You have a fixed budget and multiple candidate projects competing for capital allocation
- You need to present a risk-adjusted investment recommendation to leadership or an investment committee
- You want to stress-test assumptions with Monte Carlo simulation before committing capital
- The investment involves significant uncertainty in revenues, costs, timing, or market conditions

## Workflow Architecture

```
+-----------------------------------------------------------------+
|                 INVESTMENT ANALYSIS WORKFLOW                      |
|                                                                  |
|  +----------------+    +-------------------+                     |
|  |  STEP 1         |    |  STEP 2            |                    |
|  |  Define the     |--->|  Simulate          |                    |
|  |  Investment     |    |  Outcomes           |                    |
|  +----------------+    +-------------------+                     |
|        |                       |                                  |
|        | Cash flow model,      | NPV/IRR distributions,          |
|        | base case NPV/IRR     | risk metrics                    |
|        |                       |                                  |
|        |                       v                                  |
|        |               +-------------------+                     |
|        |               |  STEP 3            |                    |
|        +-------------->|  Structure the     |                    |
|                        |  Decision          |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                | EMV, EVPI, optimal              |
|                                | decision path                    |
|                                v                                  |
|                        +-------------------+                     |
|                        |  STEP 4            |                    |
|                        |  Optimize          |                    |
|                        |  Allocation         |                    |
|                        +-------------------+                     |
|                                |                                  |
|                                v                                  |
|                        +-------------------+                     |
|                        |  STEP 5            |                    |
|                        |  Synthesize        |                    |
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
| 1 | `foundations/spreadsheet-modeling` | Investment alternatives, cash flow projections, discount rate, time horizon | Base case NPV, IRR, payback for each alternative | Cash flow model structure feeds simulation in Step 2 |
| 2 | `simulation/monte-carlo-simulation` | Cash flow model from Step 1, uncertainty ranges for key inputs | NPV/IRR distributions, P(NPV<0), VaR, CVaR, tornado chart | Distributions inform decision tree probabilities in Step 3 |
| 3 | `probability/decision-analysis` | Decision points, probability nodes from Step 2, sequential choices | Decision tree, EMV at each node, EVPI, optimal path | EMV per alternative feeds optimization in Step 4 |
| 4 | `optimization/optimization-models` | Risk-adjusted values from Steps 2-3, budget constraints, minimum/maximum allocations | Optimal allocation across investments, shadow prices on constraints | Final allocation feeds synthesis in Step 5 |
| 5 | Synthesis | All outputs from Steps 1-4 | Final recommendation with risk metrics, sensitivity highlights | Deliverable workbook or report |

## Process

### Entry Mode Selection

When the user invokes this workflow, determine which entry mode applies:

**Guided Mode** -- User says something like "help me analyze this investment" or "walk me through the investment analysis." Run each step interactively, explaining the handoff between steps, confirming intermediate outputs before proceeding.

**Context Dump Mode** -- User provides all data (cash flows, uncertainty ranges, decision structure, budget constraints) upfront. Run all five steps in sequence, present the final workbook with a summary of decisions made at each stage.

**Quick Draft Mode** -- User says "just build the investment analysis" or provides numbers inline. Use what is given, fill reasonable defaults (state them clearly), produce the complete deliverable immediately.

### Output Mode Detection

Detect the user's preferred output mode per `references/output-mode-routing.md`:

| Mode | What Happens |
|------|-------------|
| Excel | Shortcut.ai builds an IB-formatted multi-tab workbook |
| Python | Self-contained script with numpy/scipy for simulation, PuLP for optimization, matplotlib for charts |
| Both | Python computes, Shortcut.ai formats results into Excel |
| Teach | Walk through the framework and math, no code output |

---

### Step 1: Define the Investment

**Invoke:** `foundations/spreadsheet-modeling`

**Purpose in this workflow:** Build the deterministic cash flow model that serves as the foundation for all subsequent analysis. Establish the base case before introducing uncertainty.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Investment alternatives (names, descriptions) | Yes | Ask -- no default |
| Initial investment cost per alternative | Yes | Ask -- no default |
| Projected cash flows per period | Yes | Ask -- no default |
| Time horizon (years/periods) | Yes | 5 years |
| Discount rate (WACC or hurdle rate) | Yes | 10% |
| Terminal value approach | If applicable | No terminal value |

**Actions:**
1. Build a cash flow projection for each investment alternative
2. Calculate NPV, IRR, payback period, and profitability index for each
3. Create one-way and two-way sensitivity tables on key assumptions (discount rate, revenue growth)
4. Rank alternatives by base case NPV

**Output carried forward:**
- Cash flow model structure (which cells are uncertain)
- Base case NPV, IRR, payback for each alternative
- Sensitivity ranges that identify the most impactful assumptions

**Guided mode checkpoint:** "Base case analysis is complete. Alternative A has NPV of $X, Alternative B has NPV of $Y. The model is most sensitive to [variable]. Now we'll simulate uncertainty around these key assumptions. Ready to proceed?"

---

### Step 2: Simulate Outcomes

**Invoke:** `simulation/monte-carlo-simulation`

**Purpose in this workflow:** Replace single-point estimates with probability distributions to understand the full range of possible outcomes and quantify investment risk.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Uncertain variables identified in Step 1 | Yes | Top 3 from sensitivity analysis |
| Probability distributions for each variable | Yes | Triangular (min, most likely, max) |
| Correlations between variables | If applicable | Independent |
| Number of iterations | No | 10,000 |

**Actions:**
1. Assign probability distributions to uncertain cash flow inputs
2. Run Monte Carlo simulation (10,000 iterations) for each alternative
3. Generate NPV and IRR distributions
4. Calculate risk metrics: P(NPV < 0), Value at Risk (5th percentile), CVaR, mean, median, standard deviation
5. Create tornado chart showing which variables drive the most variance
6. Overlay NPV distributions for visual comparison of alternatives

**Output carried forward:**
- NPV distribution statistics per alternative (mean, std dev, percentiles)
- P(NPV < 0) per alternative -- probability of loss
- Tornado chart rankings -- which assumptions matter most
- Distribution parameters feed probability nodes in Step 3

**Guided mode checkpoint:** "Simulation complete. Alternative A has mean NPV of $X with [Y]% chance of negative NPV. Alternative B has mean NPV of $Z with [W]% chance of negative NPV. Alternative A has higher expected value but wider uncertainty. Now we'll structure any sequential decisions. Ready to proceed?"

---

### Step 3: Structure the Decision

**Invoke:** `probability/decision-analysis`

**Purpose in this workflow:** If the investment involves sequential choices (invest now vs. defer, pilot then expand, abandon option), build a decision tree that captures the value of managerial flexibility.

**Note:** If the investment is a simple accept/reject with no sequential decisions, this step simplifies to comparing the simulation results from Step 2. Skip the full decision tree and proceed to Step 4 with the simulation-derived expected values.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| Decision points (choices available) | Yes | Accept/reject only |
| Chance nodes (uncertain events) | Yes | From Step 2 distributions |
| Probabilities for each outcome | Yes | From Step 2 simulation |
| Payoffs at terminal nodes | Yes | NPV values from Steps 1-2 |
| Cost of information (if EVPI relevant) | If applicable | Ask |

**Actions:**
1. Map the decision structure: what choices exist, in what sequence, what uncertainties resolve between choices
2. Build a decision tree with decision nodes (squares) and chance nodes (circles)
3. Calculate Expected Monetary Value (EMV) at each node using backward induction
4. Calculate EVPI (Expected Value of Perfect Information) to determine the maximum worth paying for additional research
5. Identify the optimal decision path

**Output carried forward:**
- EMV for each investment alternative (risk-adjusted, incorporating sequential flexibility)
- EVPI -- maximum value of additional market research or pilot studies
- Optimal decision path with contingent strategies
- These values feed the optimization in Step 4

**Guided mode checkpoint:** "Decision tree analysis complete. The EMV of Alternative A is $X (higher than its static NPV of $Y because of the option to expand). EVPI is $Z, meaning market research costing less than $Z is worth pursuing. Now we'll optimize the allocation across alternatives. Ready to proceed?"

---

### Step 4: Optimize Allocation

**Invoke:** `optimization/optimization-models`

**Purpose in this workflow:** If the user has multiple investment alternatives competing for a limited budget, formulate and solve an optimization model to find the capital allocation that maximizes total risk-adjusted value.

**Note:** If there is only one investment (accept/reject), this step simplifies to the go/no-go recommendation based on Steps 1-3. Skip the optimization and proceed to Step 5.

**Inputs required:**

| Input | Required | Default If Missing |
|-------|----------|-------------------|
| EMV or risk-adjusted NPV per alternative | Yes | From Steps 2-3 |
| Investment cost per alternative | Yes | From Step 1 |
| Total budget constraint | Yes | Ask -- no default |
| Minimum/maximum allocation per investment | If applicable | 0 to 100% |
| Integer constraints (all-or-nothing) | If applicable | Divisible (LP) |
| Risk constraints (max P(loss), portfolio VaR) | If applicable | None |

**Actions:**
1. Formulate the optimization: maximize total EMV subject to budget and risk constraints
2. Solve using PuLP (Python) or Solver setup (Excel)
3. Report the optimal allocation: how much to invest in each alternative
4. Calculate shadow prices on binding constraints (what is an extra dollar of budget worth?)
5. Run sensitivity on the budget constraint to show the efficient frontier

**Output carried forward:**
- Optimal investment mix with dollar allocations
- Shadow prices on constraints
- Total portfolio EMV and risk metrics
- These feed the final synthesis

**Guided mode checkpoint:** "Optimization complete. The optimal allocation is $X to Alternative A and $Y to Alternative B, yielding total portfolio EMV of $Z. The shadow price on the budget constraint is $W per additional dollar. Now I'll synthesize everything into the final recommendation."

---

### Step 5: Synthesize

**Purpose:** Consolidate all analysis into a coherent recommendation with clear risk communication.

**Actions:**
1. Summarize the recommendation: which investments to pursue, how much to allocate, contingent strategies
2. Present key risk metrics: P(loss), VaR, worst-case scenario
3. Highlight the most critical assumptions (from tornado chart)
4. State the value of flexibility (EMV with options vs. static NPV)
5. Note EVPI and whether additional research is warranted
6. Assemble the deliverable (workbook or report)

---

## Excel Output Specification

All formatting follows `references/excel-standards.md`. Inputs use blue font. Formulas use black font. Cross-sheet references use green font.

### Tab 1: Investment Summary Dashboard

| Row | Metric | Source | Format |
|-----|--------|--------|--------|
| 3 | Number of Alternatives | Count from Tab 2 | `#,##0` |
| 4 | Total Budget | Assumptions tab | `$#,##0` |
| 6 | Recommended Allocation | From Tab 5 optimization | `$#,##0` per alternative |
| 8 | Portfolio EMV | Sum of allocated EMVs | `$#,##0` |
| 9 | Portfolio P(Loss) | Weighted from Tab 3 | `0.0%` |
| 10 | Portfolio VaR (5%) | From simulation | `$#,##0` |
| 12 | EVPI | From Tab 4 | `$#,##0` |
| 14 | Top Risk Factor | From tornado | Text |

### Tab 2: Cash Flow Models

One section per alternative. Rows = periods, columns = revenue, costs, net cash flow, cumulative, NPV, IRR. All formulas reference Assumptions tab.

### Tab 3: Simulation Results

| Column | Header | Format |
|--------|--------|--------|
| A | Alternative | Text |
| B | Mean NPV | `$#,##0` |
| C | Std Dev NPV | `$#,##0` |
| D | P(NPV < 0) | `0.0%` |
| E | VaR (5th pctile) | `$#,##0` |
| F | CVaR (5th pctile) | `$#,##0` |
| G | Mean IRR | `0.0%` |
| H | Median NPV | `$#,##0` |

Below the summary table: histogram data for NPV distribution of each alternative (bin edges and frequencies).

### Tab 4: Decision Tree

Decision tree structure in tabular form: Node ID, Node Type (Decision/Chance/Terminal), Parent Node, Branch Label, Probability, Payoff, EMV. EVPI calculation below.

### Tab 5: Optimization

| Column | Header | Format |
|--------|--------|--------|
| A | Alternative | Text |
| B | EMV | `$#,##0` |
| C | Investment Required | `$#,##0` |
| D | Optimal Allocation | `$#,##0` |
| E | Selected (Y/N) | Text |
| F | Shadow Price | `$#,##0.00` |

Budget constraint and solution summary below the table.

### Tab 6: Assumptions & Inputs

All hardcoded values. Blue font, yellow background on key assumptions.

| Cell | Label | Default | Format |
|------|-------|---------|--------|
| B3 | Discount Rate (WACC) | 10% | `0.0%` |
| B4 | Time Horizon (years) | 5 | `#,##0` |
| B5 | Total Budget | (user input) | `$#,##0` |
| B6 | Simulation Iterations | 10,000 | `#,##0` |
| B8+ | Alternative-specific assumptions | (vary) | (vary) |

Named ranges: `DiscountRate` -> B3, `TimeHorizon` -> B4, `TotalBudget` -> B5, `SimIterations` -> B6.

## Python Output Specification

When Python mode is selected, produce a self-contained script using:
- `numpy` for cash flow calculations and random sampling
- `scipy.stats` for probability distributions
- `pulp` for optimization formulation and solving
- `matplotlib` for NPV distribution histograms, tornado charts, and efficient frontier plots

Print key results to console in a structured format. Generate charts as PNG files or display inline.

## Output

This workflow produces:

1. **A multi-tab formulated workbook** (Excel mode) or **self-contained Python script** with full analysis
2. **A risk-adjusted investment recommendation** with optimal allocation across alternatives
3. **NPV/IRR distributions** showing the full range of possible outcomes, not just point estimates
4. **Decision tree analysis** (if sequential choices exist) showing the value of managerial flexibility
5. **Optimization results** (if multiple alternatives compete for budget) with shadow prices on constraints
6. **Full assumption transparency** -- change any input and watch the entire analysis update

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Using single-point NPV without simulating uncertainty | Ignores risk; the "flaw of averages" means E[f(X)] != f(E[X]) | Always run Monte Carlo to understand the NPV distribution, not just the mean |
| Treating all cash flow inputs as certain when they clearly are not | Gives false confidence in the recommendation | Identify uncertain inputs in Step 1 sensitivity analysis, then simulate them in Step 2 |
| Skipping the decision tree when sequential choices exist | Undervalues investments with embedded flexibility (options to expand, defer, abandon) | Map sequential decision points and calculate EMV with flexibility vs. without |
| Optimizing on base case NPV instead of risk-adjusted EMV | Ignores the probability distribution of outcomes | Use EMV from the decision tree (which incorporates simulation results) as the objective |
| Using the same discount rate for all alternatives regardless of risk | Penalizes safe projects and subsidizes risky ones | Adjust discount rates by risk profile, or use certainty equivalents |
| Hardcoding simulation parameters in formulas | Cannot run sensitivity analysis or update assumptions | Place all assumptions on the Assumptions tab with blue font; reference via named ranges |
| Running too few simulation iterations | Distributions are noisy, percentiles are unreliable | Use at least 10,000 iterations; check convergence of mean and standard deviation |
| Ignoring correlations between uncertain inputs | Understates portfolio risk if inputs move together | Model correlations explicitly using Cholesky decomposition or rank correlation |

## Related Skills

- **Spreadsheet Modeling** (`foundations/spreadsheet-modeling/`) -- Builds the deterministic cash flow model in Step 1
- **Monte Carlo Simulation** (`simulation/monte-carlo-simulation/`) -- Simulates uncertainty around cash flow assumptions in Step 2
- **Decision Analysis** (`probability/decision-analysis/`) -- Structures sequential decisions and calculates EMV/EVPI in Step 3
- **Optimization Models** (`optimization/optimization-models/`) -- Solves the capital allocation problem in Step 4
- **Project Valuation** (`workflows/project-valuation/`) -- Sister workflow focused specifically on valuing projects with embedded real options
