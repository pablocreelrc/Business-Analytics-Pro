---
name: Spreadsheet Modeling
description: >
  Builds structured spreadsheet models with cleanly separated inputs, calculations, and outputs.
  Covers breakeven analysis, cost projections, quantity discount models, price-demand estimation,
  NPV, IRR, and sensitivity analysis with one-way and two-way data tables.
  Trigger phrases: "build a spreadsheet model", "breakeven analysis", "breakeven point",
  "cost projection", "quantity discount model", "price-demand curve", "NPV calculation",
  "IRR analysis", "sensitivity table", "data table", "one-way data table", "two-way data table",
  "what-if analysis", "model this in Excel", "build a financial model", "cost-volume-profit",
  "fixed and variable costs", "time value of money", "discount cash flows",
  "how many units to break even", "scenario analysis spreadsheet", "model structure best practices",
  "separate inputs and outputs", "auditable model", "sensitivity analysis Excel"
---

## Purpose

The foundational skill every other skill in Business-Analytics-Pro builds on. Teaches how to structure a spreadsheet model so inputs, calculations, and outputs are cleanly separated — then executes that model in Excel with IB formatting. Covers breakeven analysis, cost projections, quantity discount models, price-demand estimation, NPV, IRR, and one-way/two-way sensitivity tables.

## When to Use

- You need to determine the breakeven quantity for a product or service
- You are projecting costs over time with fixed and variable components
- You need to evaluate quantity discount schedules and determine optimal order sizes
- You want to estimate a price-demand relationship from data points
- You need to compute NPV or IRR for a series of cash flows
- You want to build sensitivity tables showing how results change across key assumptions
- You need a clean, auditable model structure for any business problem
- You are building a foundation model that other skills (simulation, optimization) will extend

## Foundation

### Model Structure Principles

Every well-built spreadsheet model follows a three-layer architecture:

1. **Inputs layer** — All hardcoded assumptions live in one place. Blue font, yellow fill. Change these to run scenarios.
2. **Calculations layer** — All formulas reference the inputs layer. Black font. Never type a number into a formula cell.
3. **Outputs layer** — Key results summarized clearly. May include charts, summary metrics, and sensitivity tables.

This separation matters because it makes the model auditable (anyone can trace a result back to its assumption), reusable (change one input and the entire model updates), and error-resistant (no magic numbers buried in formulas).

### Breakeven Analysis

Breakeven analysis determines the quantity at which total revenue equals total cost — the point where profit is zero.

**Core formula:**

```
Total Cost = Fixed Costs + (Variable Cost per Unit x Quantity)
Total Revenue = Price per Unit x Quantity
Breakeven Quantity = Fixed Costs / (Price per Unit - Variable Cost per Unit)
```

The denominator (Price - Variable Cost) is the **contribution margin per unit**. Each unit sold beyond breakeven contributes this amount directly to profit.

**Extensions:**
- Multi-product breakeven: weight contribution margins by sales mix
- Breakeven revenue: Breakeven Quantity x Price
- Target profit: Q = (Fixed Costs + Target Profit) / (Price - Variable Cost)

### Cost Projections

Cost projection models estimate future costs by separating fixed and variable components, then applying growth rates or step functions over a time horizon.

**Key structure:**

```
Total Cost(t) = Fixed Cost(t) + Variable Cost per Unit(t) x Volume(t)
Fixed Cost(t) = Base Fixed Cost x (1 + inflation_rate)^t
Variable Cost per Unit(t) = Base VC x (1 + cost_escalation)^t
```

Step costs (e.g., adding a new production line at 10,000 units) are modeled with IF logic or lookup tables.

### Quantity Discount Models

When suppliers offer price breaks at volume thresholds, the model evaluates total cost at each discount tier to find the optimal order quantity.

**Structure:**

| Quantity Range | Unit Price |
|---------------|-----------|
| 1 - 499 | $10.00 |
| 500 - 999 | $9.50 |
| 1,000+ | $8.75 |

Total cost at each tier = Unit Price x Quantity + Ordering Cost + Holding Cost. The optimal quantity minimizes total cost, which may not be at the highest discount tier if holding costs are significant.

### Price-Demand Estimation

Estimating how quantity demanded changes with price. Given two or more (price, quantity) data points, fit a linear demand curve:

```
Q = a - b x P
```

Where:
- **b** = (Q2 - Q1) / (P1 - P2) — note the sign convention; price and quantity move inversely
- **a** = Q1 + b x P1

Revenue = P x Q = P x (a - b x P). Revenue is maximized where marginal revenue equals zero:

```
P_optimal = a / (2b)
```

### Time Value of Money

**Net Present Value (NPV):**

```
NPV = SUM(CF(t) / (1 + r)^t) for t = 0, 1, 2, ..., T
```

Where CF(t) is the cash flow in period t and r is the discount rate. A positive NPV means the investment creates value above the required return.

**Internal Rate of Return (IRR):**

The discount rate that makes NPV equal to zero:

```
0 = SUM(CF(t) / (1 + IRR)^t)
```

IRR is solved iteratively. It represents the annualized return the investment generates. Compare IRR to the hurdle rate: if IRR > hurdle rate, the project adds value.

**Decision rule:** Accept projects with NPV > 0 or IRR > hurdle rate. When projects are mutually exclusive, rank by NPV (not IRR) because NPV accounts for scale.

### Sensitivity Analysis with Data Tables

**One-way data table:** Varies one input across a range and shows how one or more outputs change. Structure: input values in a column, output formula(s) in the adjacent column header(s).

**Two-way data table:** Varies two inputs simultaneously in a matrix. Row headers = Input 1 values. Column headers = Input 2 values. The cell at the intersection shows the output for that combination.

Data tables are the primary tool for understanding which assumptions drive the most variation in results.

## Process

### Output Mode Detection

Detect the user's desired output mode per `references/output-mode-routing.md`:

| Signal | Mode |
|--------|------|
| "Excel", "spreadsheet", "model", "workbook" | Excel |
| "Python", "script", "calculate", "compute" | Python |
| "both", "Excel and Python" | Both |
| "explain", "teach", "walk me through", "how does" | Teach |
| No signal | Ask: "Want me to build this in Excel, run it in Python, or walk through the concepts?" |

**Note:** Excel and Teach are the primary modes for this skill. Python and Both are atypical but supported.

### Entry Mode Selection

**Guided Mode** — The user wants to be walked through step by step:

1. "What type of model do you need? (1) Breakeven, (2) Cost projection, (3) Quantity discounts, (4) Price-demand, (5) NPV/IRR, or (6) Custom?"
2. Collect inputs based on model type:
   - Breakeven: fixed costs, variable cost per unit, price per unit, optional target profit
   - Cost projection: base fixed cost, base variable cost, volume forecast, growth rates, time horizon
   - Quantity discounts: discount schedule, ordering cost, holding cost rate, annual demand
   - Price-demand: at least two (price, quantity) data points, cost data for profit analysis
   - NPV/IRR: cash flow series, discount rate, initial investment
3. "Do you want a sensitivity table? If yes, which input(s) should vary, and over what range?"
4. Confirm all inputs, then build.

**Context Dump Mode** — The user pastes a problem, dataset, or case description:

1. Parse all numerical inputs from the text.
2. Identify the model type (breakeven, cost projection, NPV/IRR, etc.).
3. State what you found and any assumptions you are making.
4. Ask one clarifying question if a critical input is ambiguous, then build.

**Quick Draft Mode** — The user says "just build it" or provides numbers directly:

1. Take all numbers provided.
2. Fill reasonable defaults for anything missing (state defaults clearly).
3. Produce the workbook immediately.

### Adaptive Questioning

Regardless of entry mode, resolve these before building:

| Input | Required For | Default If Missing |
|-------|-------------|-------------------|
| Fixed costs | Breakeven, Cost projection | Ask — no default |
| Variable cost per unit | Breakeven, Cost projection | Ask — no default |
| Price per unit | Breakeven, Price-demand | Ask — no default |
| Cash flows | NPV/IRR | Ask — no default |
| Discount rate | NPV/IRR, Cost projection | 10% (state assumption) |
| Time horizon | Cost projection, NPV/IRR | 5 years (state assumption) |
| Growth rate | Cost projection | 0% — flat (state assumption) |
| Sensitivity variable(s) | Sensitivity table | Primary driver of the model (e.g., price for breakeven) |
| Sensitivity range | Sensitivity table | +/- 30% of base value in 5% steps |

### Build Steps (Excel Mode)

1. Create the Excel workbook using Shortcut.ai API (`shortcut_excel.py`) with tabs per the Excel Output Specification below.
2. Enter all hardcoded inputs on the Assumptions tab in blue font, yellow fill.
3. Build all formulas on the Model tab referencing the Assumptions tab (cross-sheet links in green font).
4. Build the Sensitivity tab with one-way and/or two-way data tables referencing the Assumptions tab.
5. Apply IB formatting per `references/excel-standards.md`.
6. Present key results with a brief interpretation.

### Build Steps (Teach Mode)

1. Explain the relevant framework and formulas with worked examples.
2. Walk through the math step by step using the user's numbers.
3. Explain what the results mean and which assumptions matter most.
4. No file creation.

## Excel Output Specification

### Tab 1: Model

**Layout:**

- Row 1: Sheet title (e.g., "Breakeven Analysis", "NPV Analysis")
- Row 2: Description of the model
- Row 4: Column headers (bold, white font on navy background, bottom border)
- Row 5 onward: Data rows
- Below the data: Summary metrics (bold, double border above)

**Columns vary by model type. Example for Breakeven:**

| Column | Header | Format | Formula or Input |
|--------|--------|--------|-----------------|
| A | Quantity | #,##0 | Hardcoded range (blue) |
| B | Total Revenue | #,##0 | =Quantity x Price (black formula, Price from Assumptions in green) |
| C | Fixed Costs | #,##0 | =Assumptions!FixedCosts (green link) |
| D | Variable Costs | #,##0 | =Quantity x VC_per_unit (green link to Assumptions) |
| E | Total Costs | #,##0 | =C + D (black formula) |
| F | Profit | #,##0;(#,##0) | =B - E (black formula) |

**Summary rows:**

| Label | Value | Format |
|-------|-------|--------|
| Breakeven Quantity | =FixedCosts / (Price - VC_per_unit) | #,##0, bold |
| Breakeven Revenue | =Breakeven Quantity x Price | $#,##0, bold |
| Contribution Margin | =Price - VC_per_unit | $#,##0.00 |

**Example for NPV/IRR:**

| Column | Header | Format | Formula or Input |
|--------|--------|--------|-----------------|
| A | Year | Text | 0, 1, 2, ..., T |
| B | Cash Flow | #,##0;(#,##0) | Year 0: negative initial investment (green link). Others: from Assumptions (green link) |
| C | Discount Factor | 0.0000 | =1/(1+r)^t, r from Assumptions (green link) |
| D | Present Value | #,##0;(#,##0) | =B x C (black formula) |

**Summary rows:**

| Label | Value | Format |
|-------|-------|--------|
| NPV | =SUM(D column) | $#,##0;($#,##0), bold |
| IRR | Computed via iterative formula or XIRR equivalent | 0.0%, bold |

### Tab 2: Sensitivity

**One-Way Data Table Layout:**

- Row 1: Sheet title — "Sensitivity Analysis"
- Row 3: "One-Way Sensitivity" section header (bold, light gray)
- Row 4: Column headers
- Column A: Input variable values (e.g., prices from $5 to $15)
- Column B onward: Output metrics (e.g., Breakeven Qty, NPV, Profit)
- All interior cells are formulas (black font). Header values are hardcoded (blue font).

**Two-Way Data Table Layout:**

- Below the one-way table (or on same tab with section break)
- Section header: "Two-Way Sensitivity" (bold, light gray)
- Row headers (Column A): Input 1 values (e.g., price)
- Column headers (Row): Input 2 values (e.g., variable cost)
- Corner cell: output label (e.g., "Profit")
- Interior cells: formula computing output for each (Input1, Input2) combination (black font)

### Tab 3: Assumptions

**Layout:**

- Row 1: Sheet title — "Assumptions & Inputs"
- Row 2: Description — "All hardcoded values. Change these to run scenarios."
- Organized in sections with bold sub-headers (light gray background):

**Section: Revenue Assumptions**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Price per Unit | [user input] | $#,##0.00 | Blue, yellow fill |
| Volume / Quantity | [user input] | #,##0 | Blue, yellow fill |
| Growth Rate | [user input] | 0.0% | Blue, yellow fill |

**Section: Cost Structure**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Fixed Costs | [user input] | $#,##0 | Blue, yellow fill |
| Variable Cost per Unit | [user input] | $#,##0.00 | Blue, yellow fill |
| Cost Escalation Rate | [user input] | 0.0% | Blue, yellow fill |

**Section: Valuation Parameters**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Discount Rate | [user input] | 0.0% | Blue, yellow fill |
| Time Horizon (years) | [user input] | #,##0 | Blue, yellow fill |
| Initial Investment | [user input] | $#,##0 | Blue, yellow fill |

**Section: Sensitivity Ranges**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Input 1 Min | [computed] | Matches input type | Blue |
| Input 1 Max | [computed] | Matches input type | Blue |
| Input 1 Step | [computed] | Matches input type | Blue |
| Input 2 Min | [computed] | Matches input type | Blue |
| Input 2 Max | [computed] | Matches input type | Blue |
| Input 2 Step | [computed] | Matches input type | Blue |

All cells on this tab: blue font. Yellow background on the primary scenario-driving cells. Define named ranges for every input referenced by other tabs.

### Formatting Summary

All formatting follows `references/excel-standards.md`:

- **Blue font** (0,0,255): Every hardcoded input on the Assumptions tab
- **Black font** (0,0,0): Every formula cell
- **Green font** (0,128,0): Formula cells referencing a different sheet
- **Headers**: Bold, white font on dark blue/navy, bottom border
- **Sub-headers**: Bold, light gray background
- **Negatives**: Parentheses — (1,234), never -1,234
- **Currency**: $ on first and totals rows only, no $ in body
- **Borders**: Thin bottom between sections, double bottom above totals
- **Freeze panes**: On header rows and label columns
- **Gridlines**: Off
- **No merged cells** in data ranges

**Tool:** Always use Shortcut.ai API (`shortcut_excel.py`). Never openpyxl or xlsxwriter.

## Python Output Specification

When the user requests Python mode:

```python
# Self-contained script using standard libraries (numpy, scipy)
# Structure mirrors the Excel model:
#   1. Inputs section — all assumptions as variables at the top
#   2. Calculations section — formulas referencing input variables
#   3. Outputs section — print key results, generate charts

# Required libraries: numpy, matplotlib
# Optional: scipy (for IRR solver)

# Key outputs to print:
#   - Breakeven quantity and revenue
#   - NPV, IRR
#   - Sensitivity table as formatted console output

# Charts (matplotlib):
#   - Cost-volume-profit chart for breakeven (revenue and cost lines, breakeven point marked)
#   - NPV sensitivity bar chart
#   - Two-way sensitivity heatmap
```

Use `python` (not `python3`). Include inline comments explaining methodology. Print key results to console. Generate matplotlib charts where visualization adds value.

## Output

This skill produces:

1. **A fully formulated Excel workbook** (`.xlsx`) with three tabs: Model, Sensitivity, Assumptions — or a Python script, or both, depending on the detected output mode
2. **A results summary** — e.g., "The breakeven quantity is X units at a price of $Y, requiring $Z in revenue. NPV of the 5-year projection is $A at a 10% discount rate."
3. **Interpretation** — which assumption has the greatest impact on results (based on the sensitivity table), and what the model implies for the business decision

## Anti-Patterns

| # | Mistake | Consequence | Fix |
|---|---------|-------------|-----|
| 1 | Typing numbers directly into formula cells instead of referencing the Assumptions tab | Model cannot be reused for scenarios; impossible to audit where a number came from | Every number lives on the Assumptions tab. Every formula references it. Zero hardcoded numbers in the Model or Sensitivity tabs |
| 2 | Mixing inputs and calculations on the same tab | Users cannot find which cells to change; formula cells get accidentally overwritten | Strict three-tab separation: Assumptions (inputs), Model (formulas), Sensitivity (data tables) |
| 3 | Building a sensitivity table by manually typing each scenario | Table does not update when base assumptions change; massive rework for new ranges | Use data table formulas that reference the Assumptions tab and recompute automatically |
| 4 | Using IRR as the sole decision metric for mutually exclusive projects | IRR ignores scale — a 50% return on $100 ranks above a 20% return on $1M, but the latter creates more value | Rank mutually exclusive projects by NPV. Report IRR as a supplementary metric |
| 5 | Forgetting to include the initial investment as a negative cash flow in Year 0 | NPV is overstated by the entire investment amount; project looks artificially attractive | Year 0 cash flow = negative initial investment. Discount factor for Year 0 = 1 |
| 6 | Computing breakeven with revenue per unit instead of contribution margin | Breakeven formula requires (Price - Variable Cost), not Price alone; using Price ignores variable costs entirely | Breakeven Q = Fixed Costs / (Price - Variable Cost per Unit) |
| 7 | Using a minus sign for negative values instead of parentheses | Violates IB formatting convention; inconsistent with professional financial models | Format negatives with parentheses: (1,234) not -1,234. Use format code `#,##0;(#,##0)` |
| 8 | Not defining named ranges for key assumptions | Formulas become unreadable (e.g., `=Assumptions!B7` vs. `=DiscountRate`); errors multiply as the model grows | Define a named range for every input cell on the Assumptions tab and use those names in all formulas |
| 9 | Building the sensitivity table for the wrong variable | Analysis wastes time on an input that has minimal impact on the output | Identify the highest-leverage input first (the one with the steepest sensitivity slope), then build the full table around it |
| 10 | Placing the sensitivity table on the Model tab instead of a dedicated tab | Clutters the core model; makes it harder to extend with additional scenarios | Sensitivity analysis gets its own tab, always |

## Related Skills

- **Monte Carlo Simulation** (`simulation/monte-carlo-simulation`) — Extends spreadsheet models by replacing single-point assumptions with probability distributions
- **Intro to Optimization** (`optimization/intro-to-optimization`) — Uses the model structure from this skill as the objective function and constraints for linear programming
- **Decision Analysis** (`probability/decision-analysis`) — Applies decision trees and expected monetary value to the scenarios identified in sensitivity analysis
- **Time Series Forecasting** (`forecasting/time-series-forecasting`) — Provides the demand/revenue forecasts that feed into cost projection and NPV models

This skill is a **prerequisite for all other skills** in Business-Analytics-Pro. The three-layer model structure (Inputs / Calculations / Outputs) and the sensitivity analysis techniques taught here are reused in every subsequent skill.
