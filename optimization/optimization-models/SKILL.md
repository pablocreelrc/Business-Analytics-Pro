---
name: Optimization Models
description: >
  Use this skill when the user wants to build optimization models, integer programming, IP,
  binary variables, capital budgeting, fixed cost, set covering, employee scheduling,
  workforce planning, blending problem, transportation problem, shipping cost, logistics,
  aggregate planning, production planning, portfolio optimization, Markowitz, efficient frontier,
  mean-variance, risk-return tradeoff, nonlinear programming, NLP, big-M formulation,
  assignment problem, facility location, network optimization, mixed-integer programming, MIP,
  binary decision, go/no-go, knapsack problem, budget allocation, supply chain optimization
---

# Optimization Models

## Purpose

Formulate and solve real-world optimization problems that go beyond basic LP: integer programming for yes/no decisions and indivisible quantities, transportation and logistics models, workforce scheduling, blending, aggregate planning, and nonlinear programming including Markowitz portfolio optimization. Select the right model type for the problem, build it, solve it, and interpret results.

## When to Use

- User has a capital budgeting or project selection problem (binary go/no-go decisions)
- User needs to schedule employees, shifts, or workforce across time periods
- User has a blending/mixing problem (e.g., animal feed, chemical mixtures, fuel blending)
- User needs to minimize transportation or shipping costs across origins and destinations
- User wants to optimize a financial portfolio (risk-return tradeoff, Markowitz)
- User has fixed costs that are incurred only when an activity is selected
- User needs to cover requirements with minimum cost (set covering)
- User has a nonlinear objective or constraints (diminishing returns, quadratic costs)

## Applicable Modes

| Mode | Tool | Notes |
|------|------|-------|
| Excel | Shortcut.ai API (`shortcut_excel.py`) | IB-formatted workbook with model structure |
| Python | `PuLP` (LP/IP), `scipy.optimize.minimize` (NLP), `numpy` | Self-contained script |
| Both | Python solves, Shortcut.ai formats results into Excel | Computation + presentation |
| Teach | Claude reasoning only | Walk through formulation and model selection |

**Output mode detection:** Follow `references/output-mode-routing.md`. If ambiguous, ask the user.

## Foundation

### Model Type Selection

| Problem Type | Model | Key Feature |
|-------------|-------|-------------|
| Resource allocation (continuous) | LP | All variables continuous |
| Yes/no decisions, project selection | Binary IP | Variables in {0, 1} |
| Indivisible units (people, machines) | Integer IP | Variables in Z+ |
| Mix of continuous and integer | Mixed-Integer (MIP) | Some continuous, some integer |
| Risk-return tradeoff, diminishing returns | NLP | Nonlinear objective or constraints |

### Integer Programming (IP)

**Why integer constraints matter:** LP allows x = 2.7 employees, which is meaningless. IP forces whole numbers but makes the problem computationally harder — you cannot simply round the LP solution.

**Binary variables (0/1):**
Used for yes/no decisions. x_i = 1 if project i is selected, 0 otherwise.

```
Capital Budgeting:
  Maximize  SUM(NPV_i * x_i)        for i = 1..n
  Subject to: SUM(Cost_i * x_i) <= Budget
              x_i in {0, 1}          for all i
```

**Fixed-cost formulation (Big-M):**
When an activity has a fixed cost incurred only if the activity is used at all:

```
  y_i in {0, 1}           (binary: 1 if activity i is used)
  x_i <= M * y_i          (if y_i = 0, then x_i = 0; M is a large upper bound)
  Total cost = SUM(Fixed_i * y_i + Variable_i * x_i)
```

Choose M as the tightest valid upper bound (not arbitrarily large — tight M improves solver performance).

**Set covering:**
Find the minimum-cost subset of facilities/resources that covers all requirements:

```
  Minimize  SUM(c_j * x_j)          for j = 1..n
  Subject to: SUM(a_ij * x_j) >= 1  for each requirement i
              x_j in {0, 1}
```

### Employee Scheduling

Typical structure: workers start a shift and work consecutive days/hours. Decision variables are the number of workers starting in each possible shift pattern.

```
  Minimize  SUM(Cost_s * x_s)       for each shift pattern s
  Subject to: Workers on duty in period t >= Demand_t   for each period t
              x_s >= 0, integer
```

### Blending Problems

Mix raw ingredients to meet specifications at minimum cost:

```
  Minimize  SUM(Cost_j * x_j)
  Subject to: SUM(Nutrient_ij * x_j) >= MinSpec_i   for each spec i
              SUM(Nutrient_ij * x_j) <= MaxSpec_i   for each spec i
              SUM(x_j) = TotalAmount
              x_j >= 0
```

### Transportation / Logistics

Ship goods from m origins to n destinations at minimum total cost:

```
  Minimize  SUM_i SUM_j (c_ij * x_ij)
  Subject to: SUM_j x_ij <= Supply_i     for each origin i
              SUM_i x_ij >= Demand_j     for each destination j
              x_ij >= 0
```

The constraint matrix has special structure (each variable appears in exactly one supply and one demand constraint), making these problems efficient to solve.

### Aggregate Planning

Combine production, workforce, and inventory decisions across time periods:

```
  Minimize  SUM_t (ProdCost_t * P_t + HireCost * H_t + FireCost * F_t + HoldCost * I_t)

  Subject to:
    I_t = I_{t-1} + P_t - Demand_t          (inventory balance)
    W_t = W_{t-1} + H_t - F_t               (workforce balance)
    P_t <= Capacity * W_t                     (production capacity)
    P_t, H_t, F_t, I_t, W_t >= 0
```

### Nonlinear Programming (NLP)

**When the objective or constraints are nonlinear.** Common cases:
- Diminishing returns (concave objective)
- Quadratic costs
- Portfolio optimization (quadratic risk term)

**Markowitz Portfolio Optimization:**

```
  Minimize   x' * Sigma * x                (portfolio variance)
  Subject to: x' * mu >= target_return      (minimum expected return)
              SUM(x_i) = 1                  (fully invested)
              x_i >= 0                       (no short selling, optional)
```

Where:
- **x** = vector of portfolio weights
- **Sigma** = covariance matrix of asset returns
- **mu** = vector of expected returns
- **x' * Sigma * x** = portfolio variance (risk)

**Efficient frontier:** Solve for many values of target_return to trace the set of portfolios that achieve minimum risk for each return level.

**NLP challenges:**
- Local vs global optima: NLP solvers may find a local optimum, not the global one
- Convexity: if the objective is convex (minimization) or concave (maximization) and constraints are convex, any local optimum is global
- Markowitz is a convex quadratic program — the global optimum is guaranteed

### LP vs IP vs NLP Decision Guide

```
Are all variables continuous?
  YES --> Are objective and constraints all linear?
            YES --> LP (use Solver/PuLP/linprog)
            NO  --> NLP (use scipy.optimize.minimize)
  NO  --> Are objective and constraints all linear?
            YES --> IP or MIP (use Solver/PuLP with integer constraints)
            NO  --> Mixed-Integer NLP (advanced; may need specialized solvers)
```

## Process

### Step 1: Identify Problem Type and Collect Inputs

Ask the user for (or extract from their problem description):
- Problem type: scheduling, blending, transportation, portfolio, capital budgeting, other
- Decision variables and whether they must be integer or binary
- Objective: maximize or minimize what
- Constraints: resource limits, requirements, balance equations
- Specific numerical data (costs, capacities, demands, returns, covariances)

**Entry modes:**
- **Guided:** Ask for problem type first, then walk through the specific inputs for that type
- **Context Dump:** User pastes a full problem; identify the type and extract all components
- **Quick Draft:** User gives minimal info (e.g., "optimize my portfolio with these 5 stocks"); make reasonable assumptions and flag them

### Step 2: Formulate the Model

1. Classify: LP, IP, MIP, or NLP
2. Define decision variables with names, types (continuous/integer/binary), and bounds
3. Write objective function
4. Write constraints with descriptive labels
5. For IP: verify Big-M values are tight; verify binary logic is correct
6. For NLP: verify convexity if possible (guarantees global optimum)
7. Sanity check: do units match? Are all indices covered?

### Step 3: Solve

**Excel mode:**
- Use Shortcut.ai to build the workbook
- Model Setup tab: decision variables (yellow fill, blue font), objective function, constraints
- For transportation: include an origin-destination matrix layout
- For portfolio: include covariance matrix and efficient frontier data

**Python mode:**

LP/IP (PuLP):
```python
from pulp import *

model = LpProblem("Model_Name", LpMinimize)
# Binary variable example
x = {i: LpVariable(f"x_{i}", cat='Binary') for i in projects}
# Integer variable example
y = {j: LpVariable(f"y_{j}", lowBound=0, cat='Integer') for j in shifts}
# Objective
model += lpSum([cost[i] * x[i] for i in projects])
# Constraints
for r in resources:
    model += lpSum([usage[r][i] * x[i] for i in projects]) <= capacity[r]
model.solve()
```

NLP / Portfolio (scipy):
```python
import numpy as np
from scipy.optimize import minimize

def portfolio_variance(weights, cov_matrix):
    return weights @ cov_matrix @ weights

constraints = [
    {'type': 'eq', 'fun': lambda w: np.sum(w) - 1},
    {'type': 'ineq', 'fun': lambda w: w @ expected_returns - target_return}
]
bounds = [(0, 1)] * n_assets
result = minimize(portfolio_variance, x0=initial_weights,
                  args=(cov_matrix,), method='SLSQP',
                  bounds=bounds, constraints=constraints)
```

**Both mode:** Solve in Python, then format results into Excel via Shortcut.ai.

**Teach mode:** Walk through the formulation, explain model type selection, discuss the business interpretation.

### Step 4: Interpret and Report

1. State optimal solution (variable values and objective value)
2. For IP: note any cases where the IP solution differs significantly from the LP relaxation
3. For transportation: present the shipping plan as a matrix
4. For portfolio: report weights, expected return, portfolio risk (std dev), Sharpe ratio if risk-free rate given
5. Sensitivity analysis: which constraints are binding, shadow prices, what-if scenarios
6. For portfolio: generate efficient frontier if user wants risk-return tradeoff visualization

## Excel Output Specification

Build via Shortcut.ai API (`shortcut_excel.py`). Follow `references/excel-standards.md` for all formatting.

### Tab: Model Setup
- Decision variables with optimal values (blue font, yellow fill for variable cells)
- Objective function with coefficients (blue font, yellow fill for inputs)
- Constraint matrix with LHS values, signs, and RHS values
- For transportation: origin-destination cost matrix and flow matrix
- For portfolio: expected returns vector and covariance matrix

### Tab: Optimal Solution
- Summary of optimal decision variable values
- Optimal objective function value
- For capital budgeting: selected/rejected project list with NPVs and costs
- For scheduling: staffing level by period vs demand
- For transportation: shipment matrix with origin/destination totals
- For portfolio: asset weights, expected return, portfolio std dev

### Tab: Sensitivity Analysis
- Shadow prices for each constraint with allowable ranges
- Reduced costs for each variable
- For portfolio: marginal contribution to risk for each asset
- Format shadow prices with `#,##0.00;(#,##0.00)`

### Tab: Assumptions
- All hardcoded inputs: blue font, yellow fill
- Named ranges for key parameters
- Model type and solver settings documented
- Data sources and assumption notes

### Additional Tabs (problem-specific):

**Transportation Matrix** (for transportation problems):
- Cost matrix, flow matrix, supply/demand summary

**Efficient Frontier** (for portfolio problems):
- Table: target return, min variance, std dev, portfolio weights for each point
- Chart data for plotting the efficient frontier curve
- Format returns as `0.0%`, std dev as `0.0%`

## Python Output Specification

Self-contained script using PuLP (LP/IP) or scipy.optimize.minimize (NLP).

**Required output:**
1. Print model type and problem summary
2. Print optimal decision variable values
3. Print optimal objective function value
4. Print constraint analysis (slack, binding status, shadow prices where available)
5. Problem-specific output (see below)
6. Inline comments explaining methodology

**Capital budgeting output:**
```
=== CAPITAL BUDGETING SOLUTION ===
Budget: $500,000 | Used: $485,000

Selected Projects:
  Project A: NPV = $120,000  Cost = $150,000  [SELECTED]
  Project B: NPV = $95,000   Cost = $200,000  [SELECTED]
  Project C: NPV = $60,000   Cost = $135,000  [SELECTED]
  Project D: NPV = $80,000   Cost = $180,000  [NOT SELECTED]

Total NPV: $275,000
```

**Transportation output:**
```
=== TRANSPORTATION SOLUTION ===
Total Shipping Cost: $12,450

Shipment Matrix:
              Dest1   Dest2   Dest3   Supply
  Origin1      100      50       0     150
  Origin2        0     200     100     300
  Demand       100     250     100
```

**Portfolio output:**
```
=== PORTFOLIO OPTIMIZATION ===
Target Return: 10.0%
Portfolio Std Dev: 14.2%

Asset Weights:
  Stock A:  35.2%
  Stock B:  25.8%
  Stock C:  20.0%
  Stock D:  19.0%

Expected Return: 10.0%  |  Risk (Std Dev): 14.2%  |  Sharpe Ratio: 0.49
```

For portfolio problems, generate an efficient frontier plot with matplotlib:
- X-axis: Portfolio Std Dev (%)
- Y-axis: Expected Return (%)
- Mark the optimal portfolio and individual assets

## Output

| Deliverable | Excel | Python |
|-------------|-------|--------|
| Optimal variable values | Optimal Solution tab | Console table |
| Objective function value | Optimal Solution tab | Console print |
| Constraint analysis | Sensitivity Analysis tab | Console table |
| Shadow prices | Sensitivity Analysis tab | Console table (PuLP duals) |
| Transportation matrix | Transportation Matrix tab | Console formatted matrix |
| Efficient frontier | Efficient Frontier tab + chart data | matplotlib plot |
| Asset weights | Optimal Solution tab | Console table |
| Solver-ready layout | Yes (Shortcut.ai) | N/A |

## Anti-Patterns

| # | Anti-Pattern | Why It's Wrong | Do This Instead |
|---|-------------|----------------|-----------------|
| 1 | Rounding the LP relaxation to get integer solution | Rounded solution may be infeasible or far from optimal | Solve the IP directly; LP relaxation is a bound, not a solution |
| 2 | Using arbitrarily large Big-M values | Causes numerical instability and slow solve times | Set M to the tightest valid upper bound for each constraint |
| 3 | Ignoring LP relaxation bound | The LP relaxation gives a bound on how good the IP solution can be | Compare IP objective to LP relaxation to gauge solution quality |
| 4 | Optimizing a portfolio without the covariance matrix | Using only expected returns ignores risk entirely | Always include the full covariance matrix for portfolio optimization |
| 5 | Assuming NLP found the global optimum | NLP solvers may return local optima unless the problem is convex | Verify convexity or run from multiple starting points |
| 6 | Not validating transportation balance | If total supply does not equal total demand, the model may be infeasible or need a dummy | Check supply-demand balance; add dummy origin/destination if unbalanced |
| 7 | Hardcoding data inside constraint definitions | Makes the model impossible to audit or modify | Separate data (costs, capacities) from model structure; use input cells/arrays |
| 8 | Skipping sensitivity analysis after solving | A single optimal solution without context is brittle and uninformative | Always report shadow prices, binding constraints, and what-if insights |
| 9 | Using continuous variables for inherently discrete decisions | "Hire 2.3 people" or "build 0.7 factories" is meaningless | Use integer or binary variables for indivisible decisions |
| 10 | Building the efficient frontier with too few points | A sparse frontier misses the shape of the risk-return tradeoff | Use at least 20-50 points across the return range for a smooth curve |

## Related Skills

| Skill | Relationship |
|-------|-------------|
| `intro-to-optimization` | **Requires.** Provides LP foundations, Solver basics, and sensitivity analysis concepts |
| `spreadsheet-modeling` | **Requires.** Model structure, input separation, and data table foundations |
| `monte-carlo-simulation` | **Pairs with.** Combines simulation with optimization for stochastic problems |
| `investment-analysis` | **Feeds into.** Workflow that uses optimization for capital allocation and portfolio decisions |
| `decision-analysis` | **Complements.** Decision trees for sequential choices; optimization for simultaneous allocation |
