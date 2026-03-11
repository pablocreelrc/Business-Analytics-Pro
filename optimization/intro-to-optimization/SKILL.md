---
name: Introduction to Optimization
description: >
  Use this skill when the user wants to optimize, maximize, minimize, find the best solution,
  linear programming, LP, set up Solver, shadow price, reduced cost, sensitivity analysis,
  allowable range, binding constraint, slack variable, objective function, decision variable,
  constraint formulation, product mix, production planning, multiperiod planning,
  resource allocation, capacity constraint, infeasible, unbounded, feasible region,
  graphical solution, two-variable LP, SolverTable, optimal solution, right-hand side,
  sensitivity report, basis, optimal basis, constraint RHS, marginal value
---

# Introduction to Optimization

## Purpose

Teach and build linear programming (LP) models from scratch. Recognize optimization problems, formulate them mathematically, solve them, and — most importantly — interpret sensitivity analysis to understand how robust the solution is.

## When to Use

- User has a problem with a clear objective (maximize profit, minimize cost) and limited resources
- User needs to allocate resources across competing activities
- User asks about Solver, linear programming, shadow prices, or sensitivity analysis
- User has a product mix, production scheduling, or resource allocation problem
- User wants to understand what happens if constraints or coefficients change

## Applicable Modes

| Mode | Tool | Notes |
|------|------|-------|
| Excel | Shortcut.ai API (`shortcut_excel.py`) | IB-formatted workbook with Solver-ready layout |
| Python | `scipy.optimize.linprog` or `PuLP` | Self-contained script with full solution |
| Both | Python solves, Shortcut.ai formats results into Excel | Computation + presentation |
| Teach | Claude reasoning only | Walk through formulation and interpretation |

**Output mode detection:** Follow `references/output-mode-routing.md`. If ambiguous, ask the user.

## Foundation

### What Is Optimization?

Every optimization problem has three components:

1. **Decision variables** — what you control (e.g., how many units of each product to make)
2. **Objective function** — what you want to maximize or minimize (e.g., total profit)
3. **Constraints** — what limits you (e.g., available labor hours, raw material, demand caps)

### LP Standard Form

```
Maximize (or Minimize):  z = c'x = c1*x1 + c2*x2 + ... + cn*xn

Subject to:
  a11*x1 + a12*x2 + ... + a1n*xn  <=  b1
  a21*x1 + a22*x2 + ... + a2n*xn  <=  b2
  ...
  am1*x1 + am2*x2 + ... + amn*xn  <=  bm

  x1, x2, ..., xn  >=  0
```

Where:
- **x** = vector of decision variables
- **c** = objective function coefficients (profit/cost per unit)
- **A** = constraint coefficient matrix (resource usage per unit)
- **b** = right-hand side values (resource availability)

### Graphical Solution (Two Variables)

For problems with exactly two decision variables:
1. Plot each constraint as a line; shade the feasible side
2. The feasible region is the intersection of all shaded areas
3. The optimal solution is at a corner point (vertex) of the feasible region
4. Evaluate the objective function at each corner point; the best value wins

### Sensitivity Analysis

Sensitivity analysis answers: "How much can things change before my optimal solution changes?"

**Shadow price (dual value):**
- The marginal value of one additional unit of a constraint's RHS
- Only valid within the allowable increase/decrease range
- Binding constraints have nonzero shadow prices; non-binding have zero
- Shadow price of a <= constraint is the amount the objective improves per unit increase in RHS

**Reduced cost:**
- For variables currently at zero: how much the objective coefficient must improve before that variable enters the optimal solution
- For variables already in the solution: reduced cost is zero

**Allowable ranges:**
- **Objective coefficient ranges:** How far each c_j can change (up or down) without changing which variables are in the optimal basis (the optimal values may change, but the same variables remain positive)
- **RHS ranges:** How far each b_i can change without changing which constraints are binding (the shadow price remains valid within this range)

### Special Cases

- **Infeasibility:** No point satisfies all constraints simultaneously. Check for conflicting constraints.
- **Unboundedness:** Objective can improve without limit. Usually means a constraint is missing.
- **Alternative optima:** Multiple corner points give the same optimal objective value. The solution is not unique.
- **Degeneracy:** A corner point is defined by more binding constraints than necessary. Shadow prices may not be unique.

### Multiperiod Production Models

Extend the LP to include time periods — production in period t, inventory carried to period t+1, demand met in each period. Key structure:

```
Inventory balance: I(t) = I(t-1) + Production(t) - Demand(t)   for each period t
Capacity:          Production(t) <= Capacity(t)                 for each period t
Non-negativity:    Production(t), I(t) >= 0                     for each period t
```

Objective: minimize total production + inventory holding costs across all periods.

### SolverTable Concept

SolverTable systematically varies one or two input parameters and re-solves the optimization for each value, showing how the optimal solution and objective change. It is the optimization equivalent of a sensitivity data table.

## Process

### Step 1: Identify and Collect Inputs

Ask the user for (or extract from their problem description):
- What decisions need to be made (decision variables)
- What is being maximized or minimized (objective)
- What are the constraints (resources, requirements, bounds)
- Specific numerical values for all coefficients

**Entry modes:**
- **Guided:** Ask for each component step by step
- **Context Dump:** User pastes an entire problem; extract all components
- **Quick Draft:** User says "just build it" with minimal description; make reasonable assumptions and flag them

### Step 2: Formulate the Model

1. Define decision variables with clear names and units
2. Write the objective function
3. Write each constraint with a descriptive label
4. Verify: does each variable appear where it should? Are units consistent?
5. Check non-negativity (or other bound) requirements

### Step 3: Solve

**Excel mode:**
- Use Shortcut.ai to build the workbook with Solver-ready layout
- Model Setup tab: decision variable cells (yellow fill, blue font), objective function cell, constraint LHS/RHS cells
- Include Solver parameters as notes: target cell, changing cells, constraint references

**Python mode:**
```python
# Using PuLP (preferred for LP)
from pulp import *

model = LpProblem("Problem_Name", LpMaximize)  # or LpMinimize
# Define variables
x1 = LpVariable("x1", lowBound=0)
x2 = LpVariable("x2", lowBound=0)
# Objective
model += c1*x1 + c2*x2, "Objective"
# Constraints
model += a11*x1 + a12*x2 <= b1, "Constraint_1"
model.solve()
```

```python
# Using scipy (alternative)
from scipy.optimize import linprog

# linprog minimizes, so negate c for maximization
res = linprog(c=-c, A_ub=A, b_ub=b, bounds=bounds, method='highs')
```

**Both mode:** Solve in Python, then format results into Excel via Shortcut.ai.

**Teach mode:** Walk through the formulation and solution logic. Use the graphical method for two-variable problems.

### Step 4: Interpret and Report

1. State the optimal solution (variable values and objective value)
2. Identify binding vs non-binding constraints (slack values)
3. Present sensitivity analysis: shadow prices, reduced costs, allowable ranges
4. Highlight the most valuable constraints (highest shadow prices)
5. Discuss implications: what would the user gain from more of the binding resource?

## Excel Output Specification

Build via Shortcut.ai API (`shortcut_excel.py`). Follow `references/excel-standards.md` for all formatting.

### Tab: Model Setup
| Row | Column A | Column B | Column C | ... |
|-----|----------|----------|----------|-----|
| 1 | **Decision Variables** | Var 1 Name | Var 2 Name | ... |
| 2 | Optimal Value | {value} | {value} | ... |
| 4 | **Objective Function** | | | |
| 5 | Coefficients | {c1} | {c2} | ... |
| 6 | Objective Value | {z*} | | |
| 8 | **Constraints** | LHS Value | Sign | RHS |
| 9 | Constraint 1 label | {LHS} | <= | {b1} |
| 10 | Constraint 2 label | {LHS} | <= | {b2} |

- Decision variable values: blue font, yellow fill (these are the "inputs" Solver changes)
- Objective coefficients and RHS values: blue font, yellow fill (user inputs)
- LHS values and objective value: black font (calculated)

### Tab: Solver Solution
- Optimal decision variable values
- Optimal objective function value
- Constraint slack/surplus values
- Binding indicator (Yes/No) for each constraint

### Tab: Sensitivity Report
| Constraint | Shadow Price | RHS | Allowable Increase | Allowable Decrease |
|------------|-------------|-----|--------------------|--------------------|
| ... | ... | ... | ... | ... |

| Variable | Value | Reduced Cost | Obj Coefficient | Allowable Increase | Allowable Decrease |
|----------|-------|--------------|-----------------|--------------------|--------------------|
| ... | ... | ... | ... | ... | ... |

Format shadow prices and reduced costs with `#,##0.00;(#,##0.00)`.

### Tab: Assumptions
- All hardcoded inputs: blue font, yellow fill
- Named ranges for decision variables, objective coefficients, RHS values
- Units and data source notes

## Python Output Specification

Self-contained script using PuLP (preferred) or scipy.optimize.linprog.

**Required output:**
1. Print optimal decision variable values
2. Print optimal objective function value
3. Print constraint analysis (slack, binding status)
4. Print sensitivity information (shadow prices via dual values from PuLP)
5. Inline comments explaining each section

**Example console output:**
```
=== OPTIMAL SOLUTION ===
x1 (Product A): 150.0 units
x2 (Product B): 200.0 units
Objective (Total Profit): $8,500.00

=== CONSTRAINT ANALYSIS ===
Labor Hours:    Used 480/480  [BINDING]   Shadow Price: $12.50
Raw Material:   Used 350/400  [Slack: 50] Shadow Price: $0.00

=== SENSITIVITY ANALYSIS ===
Variable    | Value | Reduced Cost | Obj Coeff Range
Product A   | 150.0 |    0.00      | [15.0, 35.0]
Product B   | 200.0 |    0.00      | [18.0, 40.0]
```

## Output

| Deliverable | Excel | Python |
|-------------|-------|--------|
| Optimal variable values | Model Setup tab | Console table |
| Objective function value | Model Setup tab | Console print |
| Binding constraint identification | Solver Solution tab | Console table |
| Shadow prices | Sensitivity Report tab | Console table |
| Reduced costs | Sensitivity Report tab | Console table |
| Allowable ranges | Sensitivity Report tab | Console table |
| Solver-ready layout | Yes (Shortcut.ai) | N/A |

## Anti-Patterns

| # | Anti-Pattern | Why It's Wrong | Do This Instead |
|---|-------------|----------------|-----------------|
| 1 | Reporting only the optimal values without sensitivity analysis | The single optimal answer is fragile; small changes in inputs may flip the solution | Always include shadow prices, reduced costs, and allowable ranges |
| 2 | Hardcoding coefficients directly in constraint formulas | Breaks auditability; impossible to do sensitivity analysis cleanly | Put all coefficients in dedicated input cells (blue font, yellow fill) |
| 3 | Ignoring non-binding constraints | Non-binding constraints still matter — they may become binding if conditions change | Report slack for every constraint; discuss which are close to binding |
| 4 | Treating shadow prices as valid for any RHS change | Shadow prices only hold within the allowable increase/decrease range | Always report the valid range alongside the shadow price |
| 5 | Forgetting non-negativity constraints | Solver may return negative values, which are physically meaningless | Always include x >= 0 bounds unless the variable genuinely can be negative |
| 6 | Confusing shadow price with reduced cost | Shadow prices are for constraints; reduced costs are for variables | Label clearly and explain the distinction |
| 7 | Using LP for problems requiring integer solutions | LP allows fractional solutions (e.g., 2.7 employees) which may be meaningless | Use integer programming when variables must be whole numbers (see optimization-models) |
| 8 | Not checking for infeasibility or unboundedness | Solver may silently return nonsense if the model is infeasible or unbounded | Always check solver status before interpreting results |
| 9 | Optimizing without validating the model first | A perfectly solved wrong model is worse than no model at all | Verify with a known scenario or sanity-check extreme values before optimizing |
| 10 | Assuming the model captures all real-world constraints | Every model is a simplification; missing constraints produce over-optimistic results | Document all assumptions explicitly in the Assumptions tab |

## Related Skills

| Skill | Relationship |
|-------|-------------|
| `spreadsheet-modeling` | **Prerequisite.** Provides the modeling foundations (input/calculation/output separation, data tables) |
| `optimization-models` | **Extended by.** Covers integer programming, transportation, portfolio, scheduling, and NLP |
| `monte-carlo-simulation` | **Pairs with.** Adds uncertainty to optimization inputs via stochastic optimization |
| `investment-analysis` | **Feeds into.** Workflow that uses optimization for capital allocation decisions |
