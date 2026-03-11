---
name: Probability Distributions
description: >
  Models business uncertainty using probability distributions — discrete and continuous.
  Selects the right distribution, computes expected value, variance, and probabilities,
  and produces Excel workbooks or Python visualizations with full calculations.
  Trigger phrases: "probability distribution", "expected value", "normal distribution",
  "binomial probability", "poisson distribution", "exponential distribution",
  "random variable", "Z-score calculation", "PDF and CDF", "variance calculation",
  "triangular distribution", "uniform distribution", "conditional probability",
  "distribution selection", "which distribution should I use", "probability model",
  "discrete vs continuous", "distribution parameters", "probability density function",
  "cumulative distribution function", "independence test", "subjective probability",
  "distribution fitting", "sample from distribution"
---

## Purpose

Teaches and executes probability distribution analysis for business decision-making. Covers random variables (discrete and continuous), distribution selection, parameter estimation, and probability computation. Produces either a fully formulated Excel workbook with IB formatting (via Shortcut.ai API) or Python output with distribution plots and calculations using numpy, scipy.stats, and matplotlib.

## When to Use

- You need to model uncertainty in a business variable (demand, defects, arrival times, project duration)
- You need to compute expected value, variance, or specific probabilities for a random variable
- You need to select the right distribution (Normal, Binomial, Poisson, Exponential, Triangular, Uniform) for a scenario
- You need Z-score calculations or Normal distribution probability lookups
- You need to compute conditional probabilities or test independence assumptions
- You need distribution parameters as inputs for a Monte Carlo simulation or decision analysis
- You need to visualize a distribution's PDF/CDF or generate random samples
- You want to understand the difference between subjective and objective probability in a business context

## Foundation

### Random Variables

A **random variable** maps outcomes of an uncertain event to numerical values. It can be:

- **Discrete** — takes countable values (number of defects, customer arrivals, success/failure counts)
- **Continuous** — takes any value in an interval (revenue, weight, time to failure)

Every random variable has a probability distribution that describes the likelihood of each possible value.

### Expected Value and Variance

The two fundamental summary statistics for any distribution:

```
Expected Value:   E(X) = SUM(xi * P(xi))              [discrete]
                  E(X) = INTEGRAL(x * f(x) dx)         [continuous]

Variance:         Var(X) = E(X^2) - [E(X)]^2
                  Var(X) = SUM((xi - mu)^2 * P(xi))    [discrete]

Standard Deviation: SD(X) = SQRT(Var(X))
```

**Properties:**
- E(aX + b) = a * E(X) + b
- Var(aX + b) = a^2 * Var(X)
- For independent X, Y: E(X + Y) = E(X) + E(Y); Var(X + Y) = Var(X) + Var(Y)

### Discrete Distributions

**Binomial Distribution** — Number of successes in n independent trials, each with probability p.

```
P(X = k) = C(n, k) * p^k * (1 - p)^(n - k)
E(X) = n * p
Var(X) = n * p * (1 - p)
```

Use when: fixed number of trials, two outcomes per trial, constant probability, independent trials. Examples: defective items in a batch, conversion rates, pass/fail counts.

**Poisson Distribution** — Number of events in a fixed interval when events occur at a constant average rate.

```
P(X = k) = (lambda^k * e^(-lambda)) / k!
E(X) = lambda
Var(X) = lambda
```

Use when: counting rare events per unit of time/space, events are independent, rate is constant. Examples: customer arrivals per hour, server errors per day, insurance claims per year.

**Discrete Uniform** — All outcomes equally likely.

```
P(X = xi) = 1 / n   for each of n outcomes
E(X) = (a + b) / 2
Var(X) = ((b - a + 1)^2 - 1) / 12   [for integers a to b]
```

### Continuous Distributions

**Normal Distribution** — The bell curve. Defined by mean (mu) and standard deviation (sigma).

```
f(x) = (1 / (sigma * sqrt(2*pi))) * e^(-(x - mu)^2 / (2 * sigma^2))

Z-score: Z = (X - mu) / sigma
P(a < X < b) = P(Z < (b - mu)/sigma) - P(Z < (a - mu)/sigma)
```

Use when: variable is the sum of many small independent effects, data is symmetric and bell-shaped. The empirical rule: ~68% within 1 SD, ~95% within 2 SD, ~99.7% within 3 SD.

**Exponential Distribution** — Time between events in a Poisson process.

```
f(x) = lambda * e^(-lambda * x)    for x >= 0
P(X <= x) = 1 - e^(-lambda * x)
E(X) = 1 / lambda
Var(X) = 1 / lambda^2
```

Use when: modeling waiting times, time to failure, interarrival times. The memoryless property: P(X > s + t | X > s) = P(X > t).

**Triangular Distribution** — Defined by minimum (a), most likely (mode, c), and maximum (b).

```
E(X) = (a + b + c) / 3
Var(X) = (a^2 + b^2 + c^2 - a*b - a*c - b*c) / 18
```

Use when: you have three-point estimates (optimistic, most likely, pessimistic) from expert judgment. Common in project management and simulation when data is scarce.

**Continuous Uniform Distribution** — All values in [a, b] equally likely.

```
f(x) = 1 / (b - a)    for a <= x <= b
E(X) = (a + b) / 2
Var(X) = (b - a)^2 / 12
```

Use when: complete ignorance within a known range; no reason to favor any value.

### Conditional Probability and Independence

```
P(A | B) = P(A and B) / P(B)

Independence: P(A and B) = P(A) * P(B)
              equivalently, P(A | B) = P(A)

Conditional Mean:  E(X | Y = y) — the expected value of X given a specific value of Y
Conditional Variance: Var(X | Y = y) — the variance of X given a specific value of Y
```

**Bayes' Theorem:**

```
P(A | B) = P(B | A) * P(A) / P(B)
```

### Subjective vs Objective Probability

- **Objective probability** — derived from historical data or theoretical models (e.g., coin flips, actuarial tables)
- **Subjective probability** — derived from expert judgment, experience, or belief when data is unavailable
- In business, most probability inputs blend both — use historical data where available, expert judgment to fill gaps
- Distribution choice itself can be subjective (choosing Triangular vs Normal) and affects downstream analysis

### Distribution Selection Guide

| Scenario | Recommended Distribution | Key Parameters |
|----------|------------------------|---------------|
| Count of successes in fixed trials | Binomial | n (trials), p (probability) |
| Count of events per time interval | Poisson | lambda (rate) |
| Symmetric, data-rich continuous variable | Normal | mu (mean), sigma (std dev) |
| Time between events | Exponential | lambda (rate) |
| Three-point expert estimate | Triangular | a (min), c (mode), b (max) |
| No information within range | Uniform | a (min), b (max) |

## Process

### Output Mode Detection

Before selecting an entry mode, determine the output format:

- **Excel mode** — User says "spreadsheet", "workbook", "Excel", or needs a reusable model with scenario toggles. Build via Shortcut.ai API (`shortcut_excel.py`) with IB formatting per `references/excel-standards.md`.
- **Python mode** — User says "plot", "chart", "visualize", "simulate", "generate samples", or needs programmatic output. Use numpy, scipy.stats, and matplotlib.
- **Both mode** — User wants calculations in Excel AND visualizations in Python. Build both.
- **Teach mode** — User says "explain", "teach", "walk me through", "how does X work". Provide conceptual explanation with formulas and a worked example. No file output unless requested.

### Entry Mode Selection

**Guided Mode** — The user wants to be walked through step by step. Ask questions in sequence:
1. "What business variable are you modeling? Describe the uncertainty."
2. "Is the variable discrete (countable) or continuous (any value in a range)?"
3. Based on the answer, recommend a distribution and ask: "Does [distribution] fit your scenario? Here is why I suggest it: [reasoning]."
4. Collect parameters: "What are the parameter values?" (Provide guidance on what each parameter means.)
5. "What probabilities do you need? (e.g., P(X > 10), P(3 < X < 7), P(X = 5))"
6. "Do you want Excel output, Python output, or both?"
7. Confirm all inputs, then build.

**Context Dump Mode** — The user pastes a problem, dataset, or scenario description. Parse the inputs:
1. Identify the distribution type from context clues (trials + probability = Binomial; rate + count = Poisson; mean + std dev = Normal; min/mode/max = Triangular).
2. Extract all numerical parameters.
3. Identify what probabilities or statistics are requested.
4. State your interpretation and any assumptions. Ask one clarifying question if a critical input is ambiguous, then build.

**Quick Draft Mode** — The user says something like "Poisson with lambda=5, compute P(X>=3)." Take the parameters provided, compute immediately, and produce output. State any defaults applied.

### Adaptive Questioning

Regardless of mode, resolve these before building:

| Input | Required For | Default If Missing |
|-------|-------------|-------------------|
| Distribution type | All | Infer from context or ask |
| Distribution parameters | All | Ask — no default |
| Specific probability queries | Calculations | Compute full PMF/PDF and common percentiles |
| Sample size | Python sampling | 10,000 |
| Confidence level | Interval calculations | 95% |
| Output format | File generation | Teach mode (no file) |

### Build Steps (Excel)

1. Create the Excel workbook via Shortcut.ai API with tabs specified in Excel Output Specification.
2. Enter all hardcoded inputs on the Assumptions tab in blue font, yellow background on key cells.
3. Build all formulas on analysis tabs referencing the Assumptions tab (cross-sheet links in green font).
4. Apply IB formatting per `references/excel-standards.md`.
5. Present results with interpretation.

### Build Steps (Python)

1. Import numpy, scipy.stats, and matplotlib.pyplot.
2. Define the distribution with scipy.stats (e.g., `stats.norm(loc=mu, scale=sigma)`).
3. Compute requested probabilities using `.pmf()`, `.pdf()`, `.cdf()`, `.sf()` as appropriate.
4. Compute E(X), Var(X), SD(X) using `.mean()`, `.var()`, `.std()`.
5. Plot PDF/PMF and CDF on a two-panel figure with clear labels.
6. If sampling requested, generate random variates with `.rvs(size=n)` and overlay histogram on the PDF.
7. Print a summary table of results.

## Excel Output Specification

### Tab 1: Distribution Parameters

**Layout:**

- Row 1: Sheet title — "Distribution Parameters"
- Row 2: Description — "Summary of the selected distribution and its properties"
- Row 4: Column headers (bold, navy background, white font)

**Columns:**

| Column | Header | Format | Source |
|--------|--------|--------|--------|
| A | Property | Text | Row labels |
| B | Value | Varies | Formulas or links to Assumptions |

**Rows:**
- Distribution Name (text, from Assumptions — green link)
- Parameter 1 name and value (green link to Assumptions)
- Parameter 2 name and value (green link to Assumptions)
- Parameter 3 name and value (if applicable)
- Expected Value E(X) (black formula)
- Variance Var(X) (black formula)
- Standard Deviation SD(X) (black formula)
- Skewness (black formula, if applicable)

### Tab 2: Probability Calculations

**Layout:**

- Row 1: Sheet title — "Probability Calculations"
- Row 2: Description — "Computed probabilities for the selected distribution"
- Row 4: Column headers

**Columns:**

| Column | Header | Format | Source |
|--------|--------|--------|--------|
| A | Query | Text | Description of calculation |
| B | Formula | Text | Formula used |
| C | Result | 0.0000 or 0.0% | Black formula |

Rows contain each probability query requested by the user, plus standard computations (mean +/- 1 SD, 2 SD intervals, key percentiles).

### Tab 3: Distribution Chart Data

**Layout:**

- Row 1: Sheet title — "Distribution Chart Data"
- Row 2: Description — "X values and corresponding probabilities for charting"
- Row 4: Column headers

**Columns:**

| Column | Header | Format | Source |
|--------|--------|--------|--------|
| A | X | #,##0.00 | Range of values (blue, hardcoded or formula-generated) |
| B | P(X) or f(x) | 0.000000 | PMF/PDF values (black formula) |
| C | F(x) | 0.000000 | CDF values (black formula) |

Generate enough x-values to cover the meaningful range of the distribution (e.g., mu +/- 4*sigma for Normal, 0 to reasonable upper bound for Poisson/Binomial).

### Tab 4: Assumptions

**Layout:**

- Row 1: Sheet title — "Assumptions & Inputs"
- Row 2: Description — "All hardcoded values. Change these to run scenarios."
- Organized in sections with bold sub-headers:

**Section: Distribution Selection**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Distribution Type | [name] | Text | Blue |

**Section: Parameters**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Parameter 1 (named) | [value] | Appropriate | Blue, yellow fill |
| Parameter 2 (named) | [value] | Appropriate | Blue, yellow fill |
| Parameter 3 (named) | [value] | Appropriate | Blue, yellow fill |

**Section: Probability Queries**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| Query lower bound | [value] | #,##0.00 | Blue |
| Query upper bound | [value] | #,##0.00 | Blue |

**Section: Chart Range**

| Row Label | Value | Format | Font |
|-----------|-------|--------|------|
| X minimum | [value] | #,##0.00 | Blue |
| X maximum | [value] | #,##0.00 | Blue |
| X step size | [value] | #,##0.00 | Blue |

All cells on this tab use blue font. Yellow background on parameter cells and query bounds.

### Formatting Summary

All formatting follows `references/excel-standards.md`:

- **Blue font** (0,0,255): Every hardcoded input on the Assumptions tab
- **Black font** (0,0,0): Every formula cell
- **Green font** (0,128,0): Formula cells that reference a different sheet
- **Negatives**: Parentheses format
- **Borders**: Around all tables, header rows highlighted
- **Freeze panes**: On header rows and label columns
- **No merged cells** in data ranges

## Python Output Specification

### Distribution Plots

Produce a two-panel figure using matplotlib:

- **Top panel**: PMF (discrete) or PDF (continuous) with shaded region for the queried probability
- **Bottom panel**: CDF with horizontal/vertical reference lines at queried values
- Title includes distribution name and parameters
- Axis labels: "X" and "P(X = x)" or "f(x)" for top; "X" and "F(x)" for bottom
- Grid on, legend if multiple elements plotted

### Probability Calculations

Print a formatted summary table:

```
Distribution: Normal(mu=100, sigma=15)
──────────────────────────────────────
E(X)           = 100.0000
Var(X)         = 225.0000
SD(X)          = 15.0000

P(X < 85)      = 0.1587
P(85 < X < 115)= 0.6827
P(X > 130)     = 0.0228
```

### Random Sample Generation

When requested:
- Generate samples using `dist.rvs(size=n, random_state=42)` for reproducibility
- Overlay histogram on PDF plot (normalized, alpha=0.3)
- Report sample mean and sample std dev alongside theoretical values

## Output

This skill produces (depending on output mode):

1. **Excel workbook** (`.xlsx`) — Four tabs as specified above, fully formulated, IB formatted, built via Shortcut.ai API
2. **Python output** — Distribution plots (PDF/CDF), probability calculations, optional random samples
3. **Interpretation** — Plain-language explanation of results: what the distribution means for the business variable, key probabilities, and how sensitive results are to parameter assumptions
4. **Distribution recommendation** — If the user was unsure which distribution to use, a justified recommendation with reasoning

## Anti-Patterns

| Mistake | Why It Is Wrong | Correct Approach |
|---------|----------------|-----------------|
| Using Normal distribution for count data | Normal is continuous and can produce negative values; count data needs Binomial or Poisson | Match the distribution to the data type: discrete for counts, continuous for measurements |
| Confusing lambda in Poisson vs Exponential | Poisson lambda = expected count per interval; Exponential lambda = rate (1/mean time between events) | Clarify the parameter's role: Poisson E(X) = lambda; Exponential E(X) = 1/lambda |
| Hardcoding probability results instead of formulas in Excel | Workbook cannot be reused for different parameters; violates IB standards | Every calculated cell must contain a formula referencing the Assumptions tab |
| Forgetting to check distribution assumptions | Applying Binomial when trials are not independent, or Poisson when rate is not constant, produces wrong answers | State and verify assumptions before selecting a distribution |
| Using Triangular when sufficient data exists for fitting | Triangular is a rough approximation for data-scarce situations; with real data, fit a proper distribution | Use Triangular only for expert three-point estimates; with data, fit Normal, Lognormal, or other empirical distribution |
| Computing P(X = x) for a continuous distribution | For continuous distributions, P(X = x) = 0 for any specific x; only intervals have nonzero probability | Use P(a < X < b) via CDF difference for continuous distributions; use PDF for density, not probability |
| Mixing up PDF and CDF | PDF gives density (not probability); CDF gives cumulative probability P(X <= x) | PDF is the curve shape; CDF is the integral. For P(a < X < b), use F(b) - F(a) |
| Ignoring the complement rule for tail probabilities | Computing P(X > k) by summing all values above k is error-prone and slow | Use P(X > k) = 1 - P(X <= k) = 1 - F(k) via the survival function |
| Not setting random seed when generating samples | Results are not reproducible across runs | Always use `random_state=42` (or user-specified seed) for reproducibility |
| Placing inputs on analysis sheets instead of Assumptions tab | Breaks the single-source-of-truth principle in Excel | All inputs on the Assumptions tab, referenced by other tabs via green-font links |

## Related Skills

- **Monte Carlo Simulation** — Uses probability distributions as inputs to simulate business outcomes across many trials. This skill is a direct prerequisite.
- **Decision Analysis** — Combines probability distributions with payoff structures for decision trees and expected value of information calculations. This skill is a direct prerequisite.
- **Simulation Models** — Distribution outputs feed directly into simulation model parameters.
- **Descriptive Statistics** — Summarize empirical data before fitting a distribution; provides the parameters needed here.
- **Hypothesis Testing** — Uses distribution theory (Normal, t, chi-squared) to test claims about population parameters.
