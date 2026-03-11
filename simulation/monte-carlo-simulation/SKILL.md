---
name: Monte Carlo Simulation
description: "Run Monte Carlo simulation, simulate uncertainty, model risk with random sampling, build stochastic model, probability distribution simulation, NPV simulation, sensitivity analysis simulation, tornado chart from simulation, flaw of averages fix, random variable modeling, confidence interval from simulation, percentile analysis, risk quantification, iteration convergence, distribution of outcomes, simulate financial model, uncertain cash flows, Monte Carlo in Excel, Monte Carlo in Python, project risk simulation, portfolio simulation, simulate investment returns"
---

# Monte Carlo Simulation

## Purpose

Build and interpret Monte Carlo simulations that replace single-point estimates with probability distributions, producing a full range of possible outcomes and their likelihoods. Covers theory (why deterministic models mislead), math (convergence, confidence intervals, sensitivity), and hands-on implementation in Excel (via Shortcut.ai) and Python (numpy/scipy/matplotlib).

## When to Use

- A financial model has uncertain inputs and you need the **distribution** of outcomes, not just one number.
- You want to quantify probability of loss, breakeven likelihood, or tail risk.
- Sensitivity analysis needs to go beyond one-variable-at-a-time to capture joint variation.
- You need to demonstrate why "plugging in the average" gives a wrong answer (the flaw of averages).
- A stakeholder asks: "What is the chance this project loses money?" or "What is the 5th-percentile NPV?"

## Foundation

### The Flaw of Averages

A deterministic model that uses the expected value of every input produces **one** output — and that output is almost never the expected value of the true output. Any nonlinear relationship (multiplication, min/max, thresholds, option-like payoffs) guarantees that E[f(X)] != f(E[X]). Monte Carlo simulation fixes this by sampling each input from its distribution, computing the output for every draw, and aggregating the results.

### How Monte Carlo Simulation Works

1. **Define input distributions.** Each uncertain variable gets a probability distribution (normal, triangular, uniform, lognormal, PERT, etc.) parameterized by data, expert judgment, or both.
2. **Sample.** For each iteration, draw one random value from every input distribution.
3. **Compute.** Pass the drawn values through the deterministic model to get one output value.
4. **Repeat.** Run N iterations (typically 1,000 to 100,000).
5. **Aggregate.** Collect all output values into a distribution. Compute summary statistics, percentiles, histograms, and sensitivity metrics.

### Key Mathematical Foundations

**Law of Large Numbers.** As the number of iterations n grows, the sample mean converges to the true expected value:

    X_bar_n  -->  E[X]   as   n --> infinity

**Standard Error.** Precision of the sample mean:

    SE = s / sqrt(n)

where s is the sample standard deviation. Doubling precision requires 4x the iterations.

**Confidence Interval for the Mean.**

    CI = X_bar  +/-  z * SE

At 95% confidence, z = 1.96. This tells you how precisely you have estimated the mean outcome, not the spread of individual outcomes.

**Coefficient of Variation.** Normalized measure of dispersion:

    CV = std_dev / mean

Useful for comparing risk across scenarios with different scales.

**Percentile Calculations.** Sort the N simulated outputs. The p-th percentile is the value below which p% of observations fall. Key percentiles: P5 (downside), P25, P50 (median), P75, P95 (upside).

**NPV Under Uncertainty.** Each cash-flow driver (revenue growth, margin, discount rate, terminal multiple, etc.) is a distribution. Each iteration produces one NPV. The collection of NPVs forms the output distribution.

### Input Distribution Selection Guide

| Situation | Distribution | Parameters |
|---|---|---|
| Symmetric with known mean and std dev | Normal | mean, std_dev |
| Positive-only, right-skewed (prices, costs) | Lognormal | mu, sigma of log |
| Expert gives min, most likely, max | Triangular | min, mode, max |
| Expert gives min, most likely, max (weighted) | PERT / Beta-PERT | min, mode, max, lambda=4 |
| Equal likelihood across a range | Uniform | min, max |
| Rare events, count data | Poisson | lambda |
| Binary outcome | Bernoulli | p |

### Determining Number of Iterations

Start with 1,000 and increase by 10x until the key summary statistics (mean, P5, P95) stabilize to within an acceptable tolerance (e.g., <1% change). For most financial models, 10,000 iterations provide good convergence. For tail-risk analysis (P1, P99), use 50,000+.

**Convergence check:** Plot the running mean of the output as a function of iteration count. When the line flattens, you have enough iterations.

### Interpreting Simulation Output

- **Mean vs. Median:** If the distribution is skewed, the median is a better "typical" outcome than the mean.
- **Standard Deviation:** Width of the output distribution = total project risk.
- **P5 / P95 Range:** The 90% confidence band for outcomes.
- **Probability of Loss:** Fraction of iterations where NPV < 0 or IRR < hurdle rate.
- **Shape:** Bimodal distributions suggest regime-dependent outcomes. Long left tails signal catastrophic downside risk.

### Sensitivity Analysis via Simulation

**Tornado Chart.** Rank inputs by their contribution to output variance. For each input, compute the correlation (Spearman rank) between the input draws and the output. The input with the highest absolute correlation drives the most risk.

**Spider Plot.** Vary one input at a time across its range (P10 to P90) while holding others at their medians. Plot the resulting output for each input on a single chart.

## Process

### Entry Mode Detection

Detect the user's entry mode from their prompt:

**Mode 1 — Guided.** User says "walk me through", "help me set up", "step by step", or asks a conceptual question. Respond interactively: ask what model they want to simulate, what inputs are uncertain, what output metric matters. Build the simulation incrementally.

**Mode 2 — Context Dump.** User pastes a model, data, or detailed spec. Parse it, identify uncertain inputs, propose distributions, confirm with the user, then build the full simulation.

**Mode 3 — Quick Draft.** User says "just build it", "simulate this", or gives a terse instruction. Use reasonable defaults (10,000 iterations, triangular distributions for inputs without specified distributions, 95% confidence) and produce the output immediately. State assumptions clearly.

### Output Mode Detection

Detect from the user's prompt which output to produce:

| Signal | Output Mode |
|---|---|
| "Excel", "spreadsheet", "workbook", "model in Excel" | **Excel** |
| "Python", "code", "script", "notebook", "plot" | **Python** |
| "both" | **Both** |
| "explain", "teach", "how does", "why", "concept" | **Teach** |
| No signal | Default to **Python**; ask if Excel is preferred |

### Simulation Workflow (All Modes)

1. **Identify the deterministic model.** What is the formula or chain of formulas that maps inputs to outputs?
2. **Tag uncertain inputs.** Which inputs are uncertain? What ranges or distributions apply?
3. **Assign distributions.** Use the Distribution Selection Guide above. Document every assumption.
4. **Set correlation structure.** If inputs are correlated (e.g., revenue and cost of goods sold), define the correlation matrix. If unknown, assume independence and state that assumption.
5. **Run simulation.** Execute N iterations. Store all input draws and output values.
6. **Compute summary statistics.** Mean, median, std dev, min, max, P5, P25, P50, P75, P95, probability of loss, CV.
7. **Build sensitivity analysis.** Tornado chart (rank correlations) and/or spider plot.
8. **Visualize.** Histogram of output, CDF, convergence plot.
9. **Interpret.** State findings in plain language: expected outcome, risk level, key drivers, probability of adverse scenarios.

## Excel Output Specification

**Tool:** Shortcut.ai API via `shortcut_excel.py`. Never use openpyxl or xlsxwriter directly.

**IB Formatting Standards:**
- Font: Calibri 10pt
- Hard-coded inputs: blue font, yellow fill
- Formulas/calculations: black font, no fill
- Links to other sheets: green font
- Headers: bold, white font on dark navy background, bottom border
- Sub-headers: bold, light gray background
- Numbers: comma thousands (#,##0), one decimal percentages (0.0%), parentheses for negatives
- Currency: $ on first row and totals only
- Gridlines off, freeze panes on headers

**Required Tabs:**

1. **Assumptions** — All input parameters with their distribution type, parameters (min/mode/max or mean/std_dev), source/rationale. Inputs in blue font on yellow fill.

2. **Simulation Input Distributions** — For each input: distribution name, parameters, descriptive statistics (mean, std dev, skewness), small histogram sparkline or data for charting.

3. **Simulation Results** — Summary statistics table: mean, median, std dev, CV, min, max, P5, P10, P25, P50, P75, P90, P95, probability of loss. Double bottom border above totals.

4. **Histogram Data** — Bin edges, frequencies, and cumulative frequencies for the primary output metric. Data formatted for chart creation.

5. **Tornado Chart Data** — Input variable names, rank correlations with output, sorted by absolute value descending. Data formatted for horizontal bar chart.

6. **Percentile Table** — Percentiles from P1 to P99 at chosen intervals (P1, P5, P10, P15, ... P95, P99) with corresponding output values.

## Python Output Specification

**Libraries:** numpy, scipy.stats, matplotlib, pandas.

**Code Structure:**

```python
import numpy as np
import scipy.stats as stats
import matplotlib.pyplot as plt
import pandas as pd

# 1. Define input distributions
# Each input: dict with 'name', 'distribution', 'params'

# 2. Simulation loop
n_iterations = 10_000
results = np.zeros(n_iterations)
input_draws = {}  # store draws for sensitivity analysis

for var in input_variables:
    input_draws[var['name']] = var['distribution'].rvs(
        *var['params'], size=n_iterations
    )

# 3. Vectorized model computation (avoid Python loops where possible)
results = model_function(**input_draws)

# 4. Summary statistics
summary = {
    'Mean': np.mean(results),
    'Median': np.median(results),
    'Std Dev': np.std(results),
    'CV': np.std(results) / np.mean(results),
    'Min': np.min(results),
    'Max': np.max(results),
    'P5': np.percentile(results, 5),
    'P25': np.percentile(results, 25),
    'P50': np.percentile(results, 50),
    'P75': np.percentile(results, 75),
    'P95': np.percentile(results, 95),
    'Prob of Loss': np.mean(results < 0),
}

# 5. Histogram
fig, ax = plt.subplots(figsize=(10, 6))
ax.hist(results, bins=50, edgecolor='black', alpha=0.7)
ax.axvline(summary['Mean'], color='red', linestyle='--', label='Mean')
ax.axvline(summary['P5'], color='orange', linestyle=':', label='P5')
ax.axvline(summary['P95'], color='orange', linestyle=':', label='P95')
ax.set_xlabel('Output Value')
ax.set_ylabel('Frequency')
ax.set_title('Monte Carlo Simulation — Output Distribution')
ax.legend()
plt.tight_layout()
plt.show()

# 6. Tornado chart (rank correlations)
correlations = {}
for name, draws in input_draws.items():
    correlations[name] = stats.spearmanr(draws, results).correlation

tornado_df = pd.DataFrame.from_dict(
    correlations, orient='index', columns=['Rank Correlation']
).sort_values('Rank Correlation', key=abs, ascending=True)

fig, ax = plt.subplots(figsize=(8, len(tornado_df) * 0.5 + 2))
colors = ['#d9534f' if v < 0 else '#5cb85c'
          for v in tornado_df['Rank Correlation']]
ax.barh(tornado_df.index, tornado_df['Rank Correlation'], color=colors)
ax.set_xlabel('Rank Correlation with Output')
ax.set_title('Tornado Chart — Sensitivity Analysis')
ax.axvline(0, color='black', linewidth=0.8)
plt.tight_layout()
plt.show()

# 7. Convergence plot
running_mean = np.cumsum(results) / np.arange(1, n_iterations + 1)
fig, ax = plt.subplots(figsize=(10, 4))
ax.plot(running_mean)
ax.set_xlabel('Iteration')
ax.set_ylabel('Running Mean')
ax.set_title('Convergence Check')
plt.tight_layout()
plt.show()

# 8. Percentile table
percentiles = list(range(1, 100))
perc_values = np.percentile(results, percentiles)
perc_df = pd.DataFrame({'Percentile': percentiles, 'Value': perc_values})
```

**Outputs produced:**
- Summary statistics printed and stored in DataFrame
- Histogram with mean and P5/P95 markers
- Tornado chart of rank correlations
- Convergence plot
- Percentile table

## Output

Regardless of mode, every simulation output must include:

1. **Assumptions documentation** — Every input distribution with rationale.
2. **Summary statistics** — Mean, median, std dev, CV, P5, P25, P50, P75, P95, probability of loss.
3. **Histogram** — Of the primary output metric.
4. **Sensitivity analysis** — Tornado chart ranking input contributions to output variance.
5. **Interpretation** — Plain-language paragraph: what the results mean, what drives risk, what actions the findings suggest.

## Anti-Patterns

1. **Flaw of Averages.** Using E[X] for every input and reporting f(E[X]) as "the answer." This systematically underestimates risk and biases expected value whenever the model is nonlinear. Always simulate; never rely on a single deterministic run.

2. **Not Enough Iterations.** Running 100 or 500 iterations and treating the results as stable. Summary statistics will jump between runs. Use at least 10,000; check convergence before trusting results.

3. **Ignoring Convergence.** Picking an iteration count without verifying that statistics have stabilized. Always produce a convergence plot or compare statistics at n and n/2.

4. **Wrong Distribution Choice.** Applying a normal distribution to a variable that cannot go negative (e.g., price, cost, demand). Use lognormal, triangular, or PERT for naturally bounded or skewed variables.

5. **Ignoring Correlations.** Treating revenue growth and cost growth as independent when they are positively correlated. Uncorrelated sampling understates joint upside/downside risk. Define a correlation matrix when inputs co-move.

6. **Reporting Only the Mean.** Delivering "expected NPV is $5M" without the distribution shape, probability of loss, or percentile range. Stakeholders need the full risk picture.

7. **Overfitting Distributions to Sparse Data.** Fitting a 4-parameter distribution to 8 data points. Use simple distributions (triangular, uniform) when data is limited; save complex fits for large datasets.

8. **Confusing Confidence Interval of the Mean with Prediction Interval.** The CI for the mean shrinks with more iterations; the prediction interval for individual outcomes does not. Do not tell a stakeholder that "the 95% CI is narrow" when they are asking about the range of possible outcomes.

9. **Hardcoding Random Seeds Without Disclosure.** Setting a fixed seed for reproducibility is fine, but presenting one seeded run as "the" answer hides sampling variability. Always disclose when a seed is set and show that results are robust across seeds.

10. **Simulating Without a Base Model.** Jumping to Monte Carlo before building and validating a deterministic model. The simulation amplifies any structural errors in the underlying model. Build, audit, and test the deterministic version first.

## Related Skills

- **probability-distributions** — Required prerequisite. Covers distribution fitting, parameter estimation, and goodness-of-fit testing that feed into simulation input specification.
- **spreadsheet-modeling** — Required prerequisite. The deterministic model that Monte Carlo wraps must be correctly structured before adding uncertainty.
- **simulation-models** — Extension. Covers advanced simulation topics: Latin Hypercube sampling, variance reduction, multi-stage simulations, real options via simulation.
- **investment-analysis** — Downstream consumer. Uses Monte Carlo output (NPV distribution, probability of loss) for go/no-go decisions.
- **project-valuation** — Downstream consumer. Embeds simulation results into DCF and comparable analysis for risk-adjusted valuation.
