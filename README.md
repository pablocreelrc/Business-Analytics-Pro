# Business-Analytics-Pro

Business analytics and decision modeling skills for Claude Code. Covers simulation, decision trees, optimization, forecasting, and data mining — with professional Excel or Python output.

## Installation

Add to your Claude Code project:

```bash
# In your project's .claude/settings.local.json
{
  "permissions": {
    "allow": ["skill:Business-Analytics-Pro/*"]
  }
}
```

## Skills

| # | Skill | Category | Description |
|---|-------|----------|-------------|
| 1 | spreadsheet-modeling | foundations | Breakeven analysis, NPV/IRR, sensitivity tables, model structure |
| 2 | probability-distributions | probability | Normal, binomial, Poisson, exponential — selection and parameterization |
| 3 | decision-analysis | probability | Decision trees, EMV, value of information, Bayes' Rule, real options |
| 4 | monte-carlo-simulation | simulation | Monte Carlo simulation, flaw of averages, tornado charts, convergence |
| 5 | simulation-models | simulation | Correlated inputs, operations/financial/marketing simulation models |
| 6 | intro-to-optimization | optimization | Linear programming, Solver, shadow prices, sensitivity analysis |
| 7 | optimization-models | optimization | Integer programming, transportation, portfolio optimization, scheduling |
| 8 | time-series-forecasting | forecasting | Moving averages, exponential smoothing, Holt's, Winters', MAPE |
| 9 | classification | data-mining | Logistic regression, decision trees, naive Bayes, ROC/AUC |
| 10 | clustering-market-basket | data-mining | K-means, hierarchical clustering, association rules, Apriori |
| 11 | investment-analysis | workflows | End-to-end: simulate → decide → optimize |
| 12 | project-valuation | workflows | Value projects with real options and managerial flexibility |

## Output Modes

Every skill detects what you want and routes accordingly:

- **Excel** — "Build me a model in Excel" → Professional IB-formatted workbook via Shortcut.ai
- **Python** — "Run a simulation" → Self-contained Python script with charts
- **Both** — "Build and run the analysis" → Python computes, Excel formats
- **Teach** — "Walk me through decision trees" → Explanation only, no code

## Prerequisites

**For Python mode:**
- numpy, scipy, pandas, matplotlib, PuLP, scikit-learn, statsmodels, mlxtend

**For Excel mode:**
- Shortcut.ai API access (`shortcut_excel.py`)

## License

MIT
