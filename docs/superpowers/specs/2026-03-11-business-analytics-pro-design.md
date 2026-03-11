# Business-Analytics-Pro — Design Spec

**Date:** 2026-03-11
**Repo:** `pablocreelrc/Business-Analytics-Pro`
**Pattern:** Mirrors `pablocreelrc/CRM-Analytics-Pro`
---

> **INTERNAL BUILD NOTE (not included in published files):**
> Knowledge was extracted from Albright & Winston *Business Analytics* 8e (Ch 1, 6, 7, 11, 12, 13, 14, 15, 16, 17) and Professor Brandao's STA 287 lecture slides (Classes 1-14). The published skills are fully self-contained — no references to chapters, textbooks, or lectures appear in any SKILL.md.

---

## 1. Vision

A public, installable Claude Code skill repo that teaches business analytics and decision modeling frameworks — the concepts, math, and business context — and produces professional output in the user's chosen format: Excel (via Shortcut.ai), Python, both, or pure explanation.

Every skill is **fully self-contained**. All concepts, formulas, and business context are embedded directly in the SKILL.md. The user needs no textbook, no course materials, no add-in software. If the skill teaches Bayes' Rule, it explains Bayes' Rule completely.

## 2. Output Mode Routing

Every skill shares the same output mode detection logic. The user's request determines the mode:

| Signal in user message | Mode | Tool |
|---|---|---|
| "build in Excel", "create a model", "spreadsheet" | **Excel** | Shortcut.ai API (`shortcut_excel.py`) |
| "run", "simulate", "compute", "calculate", "script" | **Python** | numpy, scipy, PuLP, scikit-learn, matplotlib |
| "build and run", "model + analysis", "both" | **Both** | Python computes, Shortcut.ai formats results into Excel |
| "explain", "walk me through", "how does", "teach me" | **Teach** | Claude reasoning only |

When ambiguous, ask the user: *"Want me to build this in Excel, run it in Python, or just walk through the concepts?"*

This logic lives in `references/output-mode-routing.md` and is referenced by every skill.

**Mode-specific branching:** Each skill's Process section must include build steps for each applicable mode. Not all modes apply to every skill — e.g., `spreadsheet-modeling` is inherently Excel-native, so "Both" mode may not apply. Each SKILL.md should state which modes are applicable and provide mode-specific build steps.

### Output Mode Routing Reference (references/output-mode-routing.md)

```markdown
# Output Mode Routing

Shared logic referenced by every skill in Business-Analytics-Pro.

## Detection Rules

1. Scan the user's message for mode signals (see table below)
2. If multiple signals conflict, prefer the most specific one
3. If no signal detected, ask: "Want me to build this in Excel, run it in Python, or just walk through the concepts?"

| Priority | Signal Keywords | Mode |
|----------|----------------|------|
| 1 (highest) | "both", "build and run", "Excel and Python" | Both |
| 2 | "Excel", "spreadsheet", "model", "workbook", "Shortcut" | Excel |
| 2 | "Python", "script", "run", "simulate", "compute", "calculate" | Python |
| 3 | "explain", "walk me through", "how", "teach", "what is" | Teach |
| 4 (default) | No signal | Ask user |

## Mode Behaviors

### Excel Mode
- Use Shortcut.ai API (`shortcut_excel.py`) to create a professional IB-formatted workbook
- Follow `references/excel-standards.md` for all formatting
- Assumptions tab always last, named ranges for key inputs
- All hardcoded inputs: blue font, yellow fill
- All formulas: black font
- Cross-sheet references: green font

### Python Mode
- Write a self-contained Python script using standard libraries
- Include inline comments explaining the methodology
- Print key results to console
- Generate matplotlib charts where visualization adds value
- Use `python` (not `python3`) on Windows

### Both Mode
- Python computes results first
- Shortcut.ai formats results into a professional Excel workbook
- The Excel workbook contains both the raw data and formatted output
- Not applicable for skills that are inherently single-mode (note in SKILL.md)

### Teach Mode
- Claude explains the framework, math, and business context
- Use formulas in code blocks for clarity
- Walk through the logic step by step
- No code output, no file creation
```

## 3. Repo Structure

```
Business-Analytics-Pro/
├── .gitignore
├── .claude-plugin/
│   └── marketplace.json
├── LICENSE (MIT)
├── README.md
├── CATALOG.md
├── CLAUDE.md
├── references/
│   ├── excel-standards.md
│   ├── output-mode-routing.md
│   └── analytics-format-codes.md
├── foundations/
│   └── spreadsheet-modeling/
│       └── SKILL.md
├── probability/
│   ├── probability-distributions/
│   │   └── SKILL.md
│   └── decision-analysis/
│       ├── SKILL.md
│       └── references/
│           └── bayes-rule-derivation.md
├── simulation/
│   ├── monte-carlo-simulation/
│   │   └── SKILL.md
│   └── simulation-models/
│       └── SKILL.md
├── optimization/
│   ├── intro-to-optimization/
│   │   └── SKILL.md
│   └── optimization-models/
│       └── SKILL.md
├── forecasting/
│   └── time-series-forecasting/
│       └── SKILL.md
├── data-mining/
│   ├── classification/
│   │   └── SKILL.md
│   └── clustering-market-basket/
│       └── SKILL.md
├── workflows/
│   ├── investment-analysis/
│   │   └── SKILL.md
│   └── project-valuation/
│       └── SKILL.md
└── infrastructure/
    └── skill-authoring-workflow/
        └── SKILL.md
```

**13 total:** 10 interactive skills + 2 workflows + 1 infrastructure

### CLAUDE.md Content

The repo-level CLAUDE.md defines:
- **Excel rules:** Reference to `references/excel-standards.md` for IB formatting
- **Skill conventions:** Anthropic standard frontmatter (`name` + `description`), pushy descriptions with 20+ trigger phrases, <500 line SKILL.md, three entry modes (Guided/Context Dump/Quick Draft)
- **Required SKILL.md body sections:** Purpose, When to Use, Foundation, Process (with mode-specific branching), Excel Output Specification, Python Output Specification, Output, Anti-Patterns, Related Skills
- **Output mode routing:** Reference to `references/output-mode-routing.md`
- **Python requirements:** All scripts use `python` (not `python3`), standard libraries listed in Section 6
- **Shortcut.ai integration:** All Excel output via `shortcut_excel.py`, never openpyxl/xlsxwriter
- **Source textbook:** Albright & Winston *Business Analytics* 8e — cite chapter numbers in Sources section
- **Categories:** `foundations`, `probability`, `simulation`, `optimization`, `forecasting`, `data-mining`, `workflows`, `infrastructure`

### README.md Content

- Project name and one-line description
- Installation instructions for Claude Code (how to add as a skill repo)
- Skill catalog summary table (name, category, one-liner)
- Output modes explanation (Excel/Python/Both/Teach)
- Prerequisites (Python libraries, Shortcut.ai API access for Excel mode)
- Source attribution (textbook, no course-specific content)
- License (MIT)

### CATALOG.md Format

```markdown
| # | Skill | Type | Tags |
|---|-------|------|------|
| 1 | [spreadsheet-modeling](foundations/spreadsheet-modeling/SKILL.md) | Interactive | spreadsheet, breakeven, npv, sensitivity |
| 2 | [probability-distributions](probability/probability-distributions/SKILL.md) | Interactive | probability, normal, binomial, distributions |
...
```

Columns: #, Skill (with relative link), Type (Interactive/Workflow/Infrastructure), Tags (lowercase, comma-separated).

### marketplace.json Schema

Located at `.claude-plugin/marketplace.json`:

```json
{
  "name": "Business-Analytics-Pro",
  "owner": "pablocreelrc",
  "metadata": {
    "description": "Business analytics and decision modeling skills for Claude Code",
    "version": "1.0.0",
    "source": "Albright & Winston, Business Analytics 8e"
  },
  "plugins": {
    "skills": [
      {
        "name": "spreadsheet-modeling",
        "category": "foundations",
        "path": "foundations/spreadsheet-modeling/SKILL.md",
        "description": "Spreadsheet modeling fundamentals — breakeven, NPV, sensitivity analysis"
      }
    ]
  }
}
```

### references/analytics-format-codes.md

Domain-specific Excel format codes for Business Analytics output:

| Metric | Format Code | Example |
|---|---|---|
| NPV, EMV, cash flows | `#,##0;(#,##0)` | 1,234 / (1,234) |
| Probabilities | `0.0%` | 45.3% |
| Correlation coefficients | `0.000` | 0.847 |
| Shadow prices | `#,##0.00;(#,##0.00)` | 12.50 |
| Simulation percentiles | `#,##0` | 2,500 |
| Forecast accuracy (MAPE) | `0.0%` | 8.3% |
| Logistic regression coefficients | `0.0000` | -0.3421 |
| Odds ratios | `0.00` | 2.15 |
| Lift values | `0.00` | 3.41 |
| Silhouette scores | `0.000` | 0.654 |
| Support/Confidence | `0.0%` | 12.5% |
| Z-scores | `0.00` | 1.96 |
| Standard errors | `0.0000` | 0.0234 |

## 4. Skill Inventory

### 4.1 foundations/spreadsheet-modeling

**Applicable modes:** Excel, Teach (Python and Both not typical)

**Concepts:** Spreadsheet modeling best practices, breakeven analysis, cost projections, quantity discount models, price-demand estimation, time value of money calculations.

**Math:** Breakeven: `Q = FC / (P - VC)`. NPV: `NPV = SUM(CFt / (1+r)^t)`. IRR definition and usage. Data tables for one-way and two-way sensitivity.

**Context:** The foundation every other skill builds on. How to structure a model so inputs, calculations, and outputs are cleanly separated. Why model structure matters for auditability and reuse.

**Key inputs:** Revenue assumptions, cost structure (fixed/variable), discount rate, time horizon, growth rates.

**Excel output tabs:** Assumptions, Model, Sensitivity (one-way and two-way data tables).

**Related skills:** Prerequisite for all other skills. Directly feeds into `monte-carlo-simulation` (adds uncertainty to deterministic models) and `intro-to-optimization` (adds optimization to spreadsheet models).

### 4.2 probability/probability-distributions

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** Random variables, probability distributions (discrete and continuous), conditional probability, independence, subjective vs objective probability.

**Math:**
- Expected value: `E(X) = SUM(xi * P(xi))`
- Variance: `Var(X) = E(X^2) - [E(X)]^2`
- Normal distribution: density function, Z-scores, `P(a < X < b)`
- Binomial: `P(X=k) = C(n,k) * p^k * (1-p)^(n-k)`
- Poisson: `P(X=k) = (lambda^k * e^(-lambda)) / k!`
- Exponential: `P(X <= x) = 1 - e^(-lambda*x)`
- Conditional mean and variance

**Context:** Selecting the right distribution to model business uncertainty. When to use Normal vs Triangular vs Uniform vs Discrete. How distribution choice affects simulation results.

**Key inputs:** Distribution type, parameters (mean, std dev, min/max/mode, rate), sample size, confidence level.

**Excel output tabs:** Distribution Parameters, Probability Calculations, Distribution Chart Data, Assumptions.

**Python output:** Distribution plots (PDF/CDF), probability calculations, random sample generation.

**Related skills:** Prerequisite for `monte-carlo-simulation` and `decision-analysis`. Feeds into `simulation-models` (correlated distributions).

### 4.3 probability/decision-analysis

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** Decision trees (decision nodes, chance nodes, end nodes), Expected Monetary Value (EMV), risk aversion and utility functions, value of perfect information, value of control, value of imperfect information, Bayes' Rule for probability updating, multistage decisions, real options (option to expand, abandon, defer), project flexibility.

**Math:**
- EMV: `EMV = SUM(payoff_i * P(outcome_i))`
- EVPI: `EVPI = EV(with perfect info) - EV(without info)`
- VOC: `VOC = EV(with control) - EV(without control)`
- Bayes' Rule: `P(A|B) = P(B|A) * P(A) / P(B)`
- Posterior probability calculation from prior + test reliability
- Utility functions: `U(x) = 1 - e^(-x/R)` (exponential utility)
- Certainty equivalent: the sure amount equivalent to a risky gamble
- Real option value: `Option value = Value(with flexibility) - Value(without flexibility)`

**Context:** When NPV alone isn't enough. How managerial flexibility adds value to projects. When to pay for information before deciding. How to structure sequential decisions where later choices depend on earlier outcomes and revealed information.

**Key inputs:** Decision alternatives, outcomes per alternative, probabilities, payoffs, discount rate, test cost and reliability (for VOI), risk tolerance R (for utility).

**Excel output tabs:** Decision Tree Structure, EMV Calculations, Sensitivity Analysis, Bayes Updating (if applicable), Assumptions.

**Python output:** Decision tree visualization (matplotlib), EMV rollback calculations, sensitivity charts.

**References subfolder:** `bayes-rule-derivation.md` — full derivation with prior/posterior/likelihood taxonomy.

**Related skills:** Requires `probability-distributions`. Feeds into `project-valuation` workflow. Pairs with `monte-carlo-simulation` for simulation-based decision analysis.

### 4.4 simulation/monte-carlo-simulation

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** The flaw of averages (why deterministic models mislead), probability distributions for input variables, how Monte Carlo simulation works (random sampling, iteration, convergence), sensitivity analysis via simulation (tornado charts, spider plots), input distribution selection, financial model simulation (projections under uncertainty).

**Math:**
- Law of Large Numbers: sample mean converges to expected value as n → infinity
- Standard error of simulation estimate: `SE = s / sqrt(n)`
- Confidence interval for simulation output: `mean +/- z * SE`
- Coefficient of variation: `CV = std_dev / mean`
- Percentile calculations from simulated distributions
- NPV under uncertainty: each input is a distribution, output is a distribution of NPVs

**Context:** Why "plug in the average" gives wrong answers. How to determine the number of iterations needed. How to interpret simulation output distributions — not just the mean but the shape, tails, and probability of loss.

**Key inputs:** Number of iterations (default 10,000), input variable distributions, correlation matrix (optional, default: independent), output metrics of interest, confidence level (default 95%).

**Excel output tabs:** Assumptions, Simulation Input Distributions, Simulation Results (summary statistics), Histogram Data, Tornado Chart Data, Percentile Table.

**Python output:** Simulation loop, histogram of results, tornado chart, summary statistics table, percentile analysis.

**Related skills:** Requires `probability-distributions` and `spreadsheet-modeling`. Extended by `simulation-models` (correlation, advanced applications). Feeds into `investment-analysis` and `project-valuation` workflows.

### 4.5 simulation/simulation-models

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** Operations simulation (bidding, warranty costs, production with uncertain yield), financial simulation (planning models, cash balance, investment models), marketing simulation (customer loyalty, sales models), correlated inputs (why independence is often wrong), input-input and input-output correlation, copulas.

**Math:**
- Correlation coefficient: `rho = Cov(X,Y) / (sigma_X * sigma_Y)`
- Rank correlation (Spearman) for non-linear dependencies
- Cholesky decomposition for generating correlated random variables
- Warranty cost models: `E(cost) = SUM(P(failure_t) * repair_cost_t)`
- Bidding models: optimal bid as function of cost uncertainty and competition
- Cash balance simulation with random inflows/outflows

**Context:** Moving from single-variable to multi-variable simulation. Why ignoring correlation underestimates risk. How to model realistic business scenarios where inputs move together (e.g., price and demand, costs and inflation).

**Key inputs:** Multiple input distributions, correlation matrix, simulation iterations, model type (operations/financial/marketing).

**Excel output tabs:** Assumptions, Correlation Matrix, Simulation Results, Comparison (with vs without correlation), Summary Statistics.

**Python output:** Correlated random variable generation, side-by-side histograms (independent vs correlated), scatter plots of input pairs, summary comparison table.

**Related skills:** Requires `monte-carlo-simulation` and `probability-distributions`. Feeds into `investment-analysis` workflow.

### 4.6 optimization/intro-to-optimization

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** What optimization is (objective function, decision variables, constraints), linear programming (LP), graphical solution for two-variable problems, Solver setup, sensitivity analysis (shadow prices, reduced costs, allowable ranges), the SolverTable add-in concept, infeasibility and unboundedness, product mix models, multiperiod production models.

**Math:**
- LP standard form: `Maximize c'x subject to Ax <= b, x >= 0`
- Shadow price: marginal value of relaxing a constraint by one unit
- Reduced cost: how much an objective coefficient must improve before a variable enters the solution
- Sensitivity ranges: how far coefficients can change without altering the optimal basis

**Context:** How to recognize when a business problem is an optimization problem. The difference between what you control (decision variables) and what limits you (constraints). Why sensitivity analysis matters more than the single optimal answer.

**Key inputs:** Decision variables (names, bounds), objective function coefficients, constraint matrix (LHS coefficients, RHS values, directions), maximize or minimize.

**Excel output tabs:** Model Setup (decision variables, objective, constraints), Solver Solution, Sensitivity Report, Assumptions.

**Python output:** PuLP or scipy.optimize model, optimal solution, sensitivity analysis, constraint slack values.

**Related skills:** Requires `spreadsheet-modeling`. Extended by `optimization-models`. Feeds into `investment-analysis` workflow.

### 4.7 optimization/optimization-models

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** Employee scheduling, blending problems, transportation/logistics models, aggregate planning, financial optimization (portfolio models), integer programming (capital budgeting, fixed costs, set covering), nonlinear programming (diminishing returns, portfolio optimization with Markowitz).

**Math:**
- Integer constraints: `x_i in {0, 1}` for binary, `x_i in Z+` for integer
- Fixed-cost formulation: `y_i * M >= x_i` where y is binary indicator, M is big-M
- Transportation problem: minimize total shipping cost across origins/destinations
- Markowitz portfolio: `Minimize x'Sigma*x subject to x'mu >= target_return, SUM(x) = 1`
- Set covering: minimum-cost subset that covers all requirements

**Context:** Real-world optimization across industries — workforce planning, supply chain, finance. When to use LP vs IP vs NLP. Why integer constraints make problems harder. How portfolio optimization balances risk and return.

**Key inputs:** Problem type (scheduling/blending/transport/portfolio/capital budgeting), decision variables, objective, constraints, integer requirements (if any), covariance matrix (for portfolio).

**Excel output tabs:** Model Setup, Optimal Solution, Sensitivity Analysis, Assumptions. Additional tabs vary by problem type (e.g., Transportation Matrix, Efficient Frontier for portfolio).

**Python output:** PuLP model for LP/IP, scipy.optimize.minimize for NLP, efficient frontier plot for portfolio, solution summary.

**Related skills:** Requires `intro-to-optimization`. Pairs with `monte-carlo-simulation` for stochastic optimization. Feeds into `investment-analysis` workflow.

### 4.8 forecasting/time-series-forecasting

**Applicable modes:** Excel, Python, Both, Teach

**Concepts:** Extrapolation vs econometric models, time series components (trend, seasonality, cyclical, noise), measures of forecast accuracy (MAE, RMSE, MAPE), testing for randomness (runs test, autocorrelation), moving averages, exponential smoothing (simple, Holt's for trend, Winters' for seasonality), deseasonalizing, regression-based trend models, random walk.

**Math:**
- Simple moving average: `F(t+1) = (1/k) * SUM(Y(t-k+1)...Y(t))`
- Simple exponential smoothing: `F(t+1) = alpha * Y(t) + (1-alpha) * F(t)`
- Holt's level: `L(t) = alpha * Y(t) + (1-alpha) * (L(t-1) + T(t-1))`
- Holt's trend: `T(t) = beta * (L(t) - L(t-1)) + (1-beta) * T(t-1)`
- Winters' seasonal (multiplicative): `S(t) = gamma * Y(t)/L(t) + (1-gamma) * S(t-s)`
- Seasonal indices: `SI = actual / deseasonalized`
- MAE: `(1/n) * SUM(|actual - forecast|)`
- MAPE: `(1/n) * SUM(|actual - forecast| / actual) * 100`
- RMSE: `sqrt((1/n) * SUM((actual - forecast)^2))`
- Autocorrelation: `r(k) = SUM((Y(t) - Ybar)(Y(t-k) - Ybar)) / SUM((Y(t) - Ybar)^2)`

**Context:** When to use which method — simple smoothing for stable series, Holt's when there's trend, Winters' when seasonal. How to detect seasonality. Why combining forecasts often outperforms any single method. The danger of extrapolating trends too far.

**Key inputs:** Time series data, forecast horizon, smoothing constants (or optimize), seasonality period (if applicable), method preference (or auto-select).

**Excel output tabs:** Raw Data, Forecast Calculations, Accuracy Metrics, Forecast Chart Data, Assumptions.

**Python output:** Forecast using statsmodels, accuracy metrics comparison across methods, time series plot with forecast overlay, residual analysis.

**Related skills:** Standalone skill. Can pair with `monte-carlo-simulation` for probabilistic forecasting.

### 4.9 data-mining/classification

**Applicable modes:** Python, Both, Teach (Excel less typical for ML)

**Concepts:** Supervised learning for classification, partitioning data (training/validation/test), logistic regression, neural networks, naive Bayes classifier, classification trees (CART), measuring accuracy (confusion matrix, accuracy rate, sensitivity, specificity, ROC curve, AUC), handling rare events (oversampling, cost-sensitive learning).

**Math:**
- Logistic regression: `P(Y=1) = 1 / (1 + e^(-(b0 + b1*x1 + ... + bk*xk)))`
- Log-odds (logit): `ln(P / (1-P)) = b0 + b1*x1 + ...`
- Odds ratio: `e^(bi)` — multiplicative effect on odds per unit change in xi
- Gini impurity (for tree splits): `Gini = 1 - SUM(p_i^2)`
- Entropy: `H = -SUM(p_i * log2(p_i))`
- Naive Bayes: `P(class|features) proportional to P(class) * PRODUCT(P(feature_i|class))`
- Confusion matrix metrics: `Sensitivity = TP / (TP + FN)`, `Specificity = TN / (TN + FP)`

**Context:** When classification beats rules of thumb. How to choose between logistic regression (interpretable), trees (visual), and neural nets (flexible). Why accuracy alone is misleading with imbalanced classes. Practical applications: churn prediction, credit scoring, fraud detection, customer targeting.

**Key inputs:** Dataset (or description of features/target), classification method, train/test split ratio (default 70/30), target variable, feature set.

**Excel output tabs:** Model Coefficients, Confusion Matrix, Accuracy Metrics, ROC Data, Assumptions.

**Python output:** scikit-learn model training, confusion matrix, ROC curve plot, feature importance, classification report.

**Related skills:** Standalone. Pairs with `clustering-market-basket` for unsupervised counterpart.

### 4.10 data-mining/clustering-market-basket

**Applicable modes:** Python, Both, Teach (Excel less typical for ML)

**Concepts:** Unsupervised learning, distance measures (Euclidean, Manhattan, standardization), K-means clustering (algorithm, choosing K, interpreting clusters), hierarchical clustering (agglomerative, dendrograms), market basket analysis (association rules, Apriori algorithm).

**Math:**
- Euclidean distance: `d = sqrt(SUM((x_i - y_i)^2))`
- K-means objective: minimize within-cluster sum of squares `SUM_k SUM_i ||x_i - mu_k||^2`
- Silhouette score: `s(i) = (b(i) - a(i)) / max(a(i), b(i))`
- Support: `P(A and B)` — how frequently items appear together
- Confidence: `P(B|A) = P(A and B) / P(A)`
- Lift: `P(B|A) / P(B)` — how much more likely B is given A vs baseline
- Apriori principle: if an itemset is infrequent, all its supersets are infrequent

**Context:** Customer segmentation without predefined labels. How many clusters is "right" (elbow method, silhouette). What lift > 1 means for cross-selling. When market basket analysis reveals non-obvious product affinities.

**Key inputs:** Dataset, number of clusters (or auto-select via elbow/silhouette), distance metric, minimum support/confidence thresholds (for association rules).

**Excel output tabs:** Cluster Assignments, Cluster Profiles (centroids), Association Rules Table, Assumptions.

**Python output:** K-means with elbow plot, cluster visualization (2D PCA), dendrogram, association rules table with support/confidence/lift, top rules summary.

**Related skills:** Standalone. Pairs with `classification` for supervised counterpart.

### 4.11 workflows/investment-analysis (chains simulation → decision-analysis → optimization)

**Applicable modes:** All four

**Purpose:** Guide the user through a complete investment analysis combining multiple analytical methods.

**Workflow steps:**
1. **Define the investment** — Collect inputs: cash flows, cost structure, time horizon, uncertainties (invokes `spreadsheet-modeling` for structure)
2. **Simulate outcomes** — Run Monte Carlo simulation on the investment model to get a distribution of NPV/IRR (invokes `monte-carlo-simulation`)
3. **Structure the decision** — If there are sequential choices or information-gathering opportunities, build a decision tree with EMV/EVPI analysis (invokes `decision-analysis`)
4. **Optimize allocation** — If choosing among multiple investments or allocating capital, formulate and solve the optimization (invokes `optimization-models`)
5. **Synthesize** — Combine results into a final recommendation with risk metrics

**Final deliverable:** Multi-tab workbook (Excel mode) or comprehensive Python report with simulation results, decision tree, and optimization solution. Each step produces intermediate output that feeds the next.

**Key inputs:** Investment alternatives, cash flow projections, uncertainty ranges, decision points, budget constraints.

**Related skills:** Chains `spreadsheet-modeling` → `monte-carlo-simulation` → `decision-analysis` → `optimization-models`.

### 4.12 workflows/project-valuation (chains probability → monte-carlo → decision-analysis)

**Applicable modes:** All four

**Purpose:** Guide the user through valuing a project that has embedded managerial flexibility (real options).

**Workflow steps:**
1. **Define the project** — Collect inputs: base case cash flows, investment cost, time horizon (invokes `spreadsheet-modeling`)
2. **Model uncertainty** — Select and parameterize probability distributions for key uncertain variables (invokes `probability-distributions`)
3. **Simulate base case** — Run Monte Carlo simulation to get NPV distribution without flexibility (invokes `monte-carlo-simulation`)
4. **Identify and value flexibility** — Map the managerial options (expand, abandon, defer, switch), build decision tree, calculate option value (invokes `decision-analysis`)
5. **Compare** — Show NPV(without flexibility) vs NPV(with flexibility) to quantify the value of real options

**Final deliverable:** Multi-tab workbook or Python report showing base case NPV distribution, decision tree with option values, and the incremental value of flexibility.

**Key inputs:** Project cash flows, investment cost, option types available, trigger conditions for each option, underlying uncertainty distributions.

**Related skills:** Chains `spreadsheet-modeling` → `probability-distributions` → `monte-carlo-simulation` → `decision-analysis`.

## 5. Shared References

### 5.1 references/excel-standards.md

IB formatting rules from the global CLAUDE.md, codified for Shortcut.ai:
- Font: Calibri 10pt
- Hardcoded inputs: blue font (0,0,255), yellow cell fill
- Formulas/calculations: black font (0,0,0), no fill
- Cross-sheet links: green font (0,128,0)
- Headers: bold, white font on dark blue/navy background, bottom border
- Sub-headers: bold, light gray background
- Number formatting: commas for thousands (#,##0), one decimal for percentages (0.0%), parentheses for negatives
- Currency: no $ in body rows, only first row and totals
- Borders: thin bottom between sections, double bottom above totals
- Column A: row labels, left-aligned; data columns: right-aligned
- Gridlines off, print area set, freeze panes on headers
- Assumptions tab always last, named ranges for all key inputs
- Domain-specific formats per `references/analytics-format-codes.md`

### 5.2 references/output-mode-routing.md

Full content specified in Section 2 above.

### 5.3 references/analytics-format-codes.md

Domain-specific format codes specified in Section 3 above.

## 6. Python Libraries by Skill

| Skill | Libraries |
|---|---|
| spreadsheet-modeling | — (Excel-only or teach) |
| probability-distributions | `numpy`, `scipy.stats`, `matplotlib` |
| decision-analysis | `numpy`, `matplotlib` (tree visualization) |
| monte-carlo-simulation | `numpy`, `scipy.stats`, `matplotlib`, `pandas` |
| simulation-models | `numpy`, `scipy.stats`, `matplotlib`, `pandas` |
| intro-to-optimization | `scipy.optimize.linprog`, `PuLP` |
| optimization-models | `PuLP`, `scipy.optimize.minimize`, `numpy` |
| time-series-forecasting | `pandas`, `statsmodels`, `matplotlib` |
| classification | `scikit-learn`, `pandas`, `matplotlib` |
| clustering-market-basket | `scikit-learn`, `mlxtend` (association rules), `pandas`, `matplotlib` |

## 7. Anti-Pattern Themes (cross-cutting)

These apply across all skills and should be included in each SKILL.md's anti-patterns table, adapted to the specific context:

1. **Using the mean instead of simulating** — the flaw of averages
2. **Ignoring correlation between inputs** — underestimates risk
3. **Not enough simulation iterations** — unstable results
4. **Hardcoding numbers in formulas** — breaks auditability
5. **Optimizing without sensitivity analysis** — single point answer is fragile
6. **Overfitting classification models** — good training accuracy, poor validation
7. **Choosing K arbitrarily in clustering** — use elbow/silhouette methods
8. **Confusing confidence with lift** in market basket analysis
9. **Extrapolating time series trends indefinitely** — mean reversion
10. **Ignoring the value of waiting** — real options matter when uncertainty is high

## 8. Build Order

Phase 1 — Infrastructure:
1. Create repo (`gh repo create`), .gitignore, LICENSE (MIT), README.md, CLAUDE.md
2. CATALOG.md, `.claude-plugin/marketplace.json`
3. `references/excel-standards.md`, `references/output-mode-routing.md`, `references/analytics-format-codes.md`
4. `infrastructure/skill-authoring-workflow/SKILL.md`

Phase 2 — Core skills (dependency order):
5. `foundations/spreadsheet-modeling/` (foundation for everything)
6. `probability/probability-distributions/` (needed by simulation and decision skills)
7. `probability/decision-analysis/` (builds on probability, needed by workflows)
8. `simulation/monte-carlo-simulation/` (uses distributions)
9. `simulation/simulation-models/` (extends monte-carlo with correlation)
10. `optimization/intro-to-optimization/` (standalone foundations)
11. `optimization/optimization-models/` (extends intro)

Phase 3 — Extended skills:
12. `forecasting/time-series-forecasting/`
13. `data-mining/classification/`
14. `data-mining/clustering-market-basket/`

Phase 4 — Workflows:
15. `workflows/investment-analysis/`
16. `workflows/project-valuation/`

Phase 5 — Testing & polish:
17. Test each skill with sample invocations across all applicable output modes
18. Final CATALOG.md and marketplace.json updates
19. README.md finalization
