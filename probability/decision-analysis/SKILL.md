---
name: Decision Analysis
description: "Build decision trees, compute EMV, value of information, Bayes updating, utility functions, real options, and multistage decisions. Use: decision tree, expected monetary value, EMV, EVPI, value of perfect information, value of imperfect information, value of control, VOC, Bayes rule, posterior probability, probability updating, risk aversion, utility function, certainty equivalent, real option, option to expand, option to abandon, option to defer, multistage decision, sequential decision, decision node, chance node, rollback, decision analysis model, flexibility value, project flexibility, managerial flexibility, pay for information, test reliability"
---

# Decision Analysis

## Purpose

Provide a complete decision-analysis toolkit that goes beyond simple NPV. This skill builds decision trees, computes Expected Monetary Value via rollback, quantifies the value of information (perfect and imperfect), applies Bayes' Rule to update probabilities from test results, incorporates risk aversion through utility functions, and values managerial flexibility (real options). All outputs are delivered in Excel (via Shortcut.ai API with IB formatting) or Python (numpy + matplotlib), or both, depending on the user's request.

## When to Use

- A decision has multiple alternatives with uncertain outcomes.
- You need to decide whether to pay for additional information (survey, test, consultant) before committing.
- Sequential decisions exist where later choices depend on earlier outcomes or revealed information.
- Risk tolerance matters and a simple EMV maximization is insufficient.
- Managerial flexibility (expand, abandon, defer) adds value beyond static NPV.
- You need to update prior beliefs with new evidence using Bayes' Rule.
- Stakeholders need a visual decision tree with clear rollback logic.
- Sensitivity analysis is required to see how changes in probabilities or payoffs shift the optimal decision.

## Foundation

### Core Structures

**Decision Tree Elements:**
- **Decision node (square):** A point where the decision-maker chooses among alternatives.
- **Chance node (circle):** A point where nature determines the outcome according to probabilities.
- **End node (triangle):** A terminal payoff.

**Rollback procedure:** Evaluate the tree from right to left. At chance nodes, compute the probability-weighted average of downstream values. At decision nodes, select the alternative with the highest value (or highest expected utility if risk-averse).

### Mathematical Framework

**Expected Monetary Value (EMV):**

```
EMV(alternative) = SUM(payoff_i * P(outcome_i))   for all outcomes i
```

Choose the alternative with the highest EMV (risk-neutral case).

**Expected Value of Perfect Information (EVPI):**

```
EVPI = EV(with perfect information) - EV(without information)
```

Where EV(with perfect information) = SUM over states [ P(state_j) * max payoff in state_j ].

EVPI is the absolute ceiling on what any information source is worth.

**Value of Control (VOC):**

```
VOC = EV(with control) - EV(without control)
```

Where EV(with control) = max payoff across all state/action combinations (you pick the state AND the action). VOC >= EVPI always.

**Value of Imperfect Information:**

Uses Bayes' Rule to update probabilities based on test reliability, then computes the expected value of the optimal strategy given the test result.

```
Value of imperfect info = EV(with test) - EV(without test) - cost of test
```

**Bayes' Rule:**

```
P(A|B) = P(B|A) * P(A) / P(B)
```

Where:
- P(A) = prior probability of state A.
- P(B|A) = likelihood: probability of observing signal B given state A is true.
- P(B) = marginal likelihood = SUM over all states [ P(B|state_k) * P(state_k) ].
- P(A|B) = posterior probability of state A after observing signal B.

See `references/bayes-rule-derivation.md` for the full derivation and worked example.

**Posterior from test reliability:**

Given test reliability (true positive rate and true negative rate), compute the joint probability table, then normalize to get posteriors for each test outcome.

**Utility Functions (Risk Aversion):**

Exponential utility:

```
U(x) = 1 - e^(-x / R)
```

Where R is the risk-tolerance parameter (higher R = less risk-averse).

**Certainty Equivalent (CE):**

The certain dollar amount that gives the same expected utility as the gamble:

```
CE = -R * ln(1 - E[U(x)])
```

The risk premium = EMV - CE. A risk-averse decision-maker may choose a lower-EMV alternative if its certainty equivalent is higher.

**Real Options:**

```
Option value = Value(with flexibility) - Value(without flexibility)
```

Types:
- **Option to expand:** Invest more if early signals are positive.
- **Option to abandon:** Cut losses if early signals are negative.
- **Option to defer:** Wait for information before committing.

Model as multistage decision trees where the second-stage decision is contingent on first-stage outcomes.

### Key Inputs

| Input | Description | Typical Source |
|-------|-------------|----------------|
| Decision alternatives | Actions available at each decision node | Problem definition |
| Outcomes | Possible states of nature at each chance node | Domain analysis |
| Probabilities | P(outcome) for each chance branch; must sum to 1.0 | Historical data, expert judgment |
| Payoffs | Dollar value at each end node | Financial projections |
| Discount rate | For multistage trees with time gaps between stages | WACC or hurdle rate |
| Test cost | Cost of acquiring information | Vendor quote, internal estimate |
| Test reliability | True positive / true negative rates | Historical accuracy data |
| Risk tolerance R | Exponential utility parameter | Calibrated via lottery questions |

## Process

### Entry Mode Detection

Detect the user's entry mode from their prompt:

**Mode 1 -- Guided (user says "help me decide," "walk me through," or provides incomplete info):**
1. Ask for: (a) the decision to be made, (b) alternatives, (c) key uncertainties, (d) estimated payoffs and probabilities, (e) whether risk aversion matters, (f) whether information purchase is an option.
2. Build the tree structure collaboratively before computing.
3. Present results with interpretation.

**Mode 2 -- Context Dump (user provides a full scenario, case, or data block):**
1. Parse alternatives, outcomes, probabilities, and payoffs from the provided text.
2. Identify whether the problem involves single-stage EMV, value of information, Bayes updating, utility, multistage decisions, or real options.
3. Build the complete analysis and present results.

**Mode 3 -- Quick Draft (user gives a direct command like "calculate EMV for..." or "build a decision tree for..."):**
1. Execute the specific calculation or build requested.
2. Deliver output immediately with minimal preamble.

### Output Mode Detection

Determine the output format from the user's prompt:

- **"Excel" / "spreadsheet" / "model"** --> Excel mode via Shortcut.ai API.
- **"Python" / "code" / "visualize" / "plot"** --> Python mode with numpy + matplotlib.
- **"Both"** --> Deliver both Excel and Python outputs.
- **"Teach" / "explain" / "how does" / "walk me through the math"** --> Teaching mode: show derivations, annotate each step, no file output unless requested.
- **No explicit mode** --> Default to Both (Excel model + Python visualization).

### Analysis Workflow

1. **Structure the tree:** List all decision nodes, chance nodes, and end nodes. Confirm branch probabilities sum to 1.0 at every chance node.
2. **Compute EMV via rollback:** Work right-to-left. At chance nodes, compute weighted average. At decision nodes, take the max.
3. **Identify optimal strategy:** The set of decisions at each decision node that yields the highest EMV (or CE if risk-averse).
4. **Compute EVPI:** Calculate the expected value with perfect information and subtract the no-information EMV.
5. **If information purchase is relevant:** Apply Bayes' Rule to update probabilities, re-solve the tree for each possible test outcome, compute the expected value with the test, and determine whether the test is worth its cost.
6. **If risk aversion is relevant:** Convert all payoffs to utilities, roll back in utility space, compute certainty equivalents, and compare the risk-averse optimal strategy to the risk-neutral one.
7. **If real options are present:** Model the multistage tree with contingent second-stage decisions. Compare the flexible strategy value to the inflexible (commit-now) value.
8. **Sensitivity analysis:** Vary key probabilities and payoffs (+/- range) and show how the optimal decision and EMV change.

## Excel Output Specification

**Tool:** Shortcut.ai API via `shortcut_excel.py`. Never use openpyxl, xlsxwriter, or manual Python Excel libraries.

**IB Formatting Standards:**
- Font: Calibri 10pt.
- Hard-coded inputs: blue font, yellow cell fill.
- Formulas/calculations: black font, no fill.
- Links to other sheets: green font.
- Headers: bold, white font on dark navy background, bottom border.
- Sub-headers: bold, light gray background.
- Numbers: comma-separated (#,##0), one decimal for percentages (0.0%), parentheses for negatives.
- Currency: $ symbol only on first data row and totals row.
- Thin bottom borders between sections; double bottom border above totals.
- Column A: row labels, left-aligned. Data columns: right-aligned.
- Gridlines off, print area set, freeze panes on headers.

**Tab Structure:**

| Tab | Contents |
|-----|----------|
| Decision Tree Structure | Tree layout with node IDs, node types (Decision/Chance/End), parent node, branch label, branch probability, payoff. Visual representation of the tree logic. |
| EMV Calculations | Rollback table showing each node's computed value, the optimal branch at each decision node, and the overall optimal strategy with final EMV. |
| Sensitivity Analysis | Data table varying 1-2 key parameters. Shows EMV for each alternative across the parameter range. Highlights crossover points where the optimal decision changes. |
| Bayes Updating | (If applicable) Prior probabilities, likelihoods, joint probabilities, marginal probabilities, posterior probabilities. Pre-posterior analysis with expected value of the test. |
| Assumptions | All inputs listed with sources/justifications. Key assumptions flagged. |

## Python Output Specification

**Libraries:** numpy, matplotlib.

**Outputs:**

1. **Decision tree visualization (matplotlib):**
   - Decision nodes as squares, chance nodes as circles, end nodes as triangles.
   - Branch labels show the action or outcome name plus probability (for chance branches).
   - End nodes show payoff values.
   - Optimal path highlighted in bold or a distinct color.
   - Title, legend, and clean layout with no overlapping text.

2. **EMV rollback table:** Printed to console or returned as a dictionary/DataFrame showing each node's rolled-back value.

3. **Sensitivity chart (matplotlib):**
   - Line chart with the varied parameter on the x-axis and EMV on the y-axis.
   - One line per alternative.
   - Vertical dashed line at the crossover point (if any).
   - Axis labels, title, legend.

4. **Bayes updating table:** (If applicable) Printed or plotted showing prior vs. posterior probabilities.

**Code standards:**
- Numpy for all numerical computations.
- Matplotlib for all visualizations; no seaborn or plotly unless user requests.
- Functions are modular: `build_tree()`, `rollback_emv()`, `compute_evpi()`, `bayes_update()`, `plot_tree()`, `sensitivity_analysis()`.
- All parameters defined at the top of the script for easy modification.
- Comments explaining each computation step.

## Output

Regardless of mode, every analysis must include:

1. **Optimal strategy statement:** One sentence naming the best decision at each decision node and the resulting EMV (or CE).
2. **Decision tree:** Visual (Python) or tabular (Excel) representation.
3. **EMV summary:** Table showing each alternative's EMV.
4. **EVPI:** Dollar value and interpretation ("you should pay at most $X for perfect information").
5. **Sensitivity insight:** Which parameter, if changed, would flip the decision -- and by how much.
6. **Risk aversion note:** (If applicable) How the optimal decision changes under risk aversion with the given R.
7. **Information value:** (If applicable) Whether the test/survey is worth purchasing, with the net value of imperfect information.
8. **Real option value:** (If applicable) Dollar value of flexibility and which option type drives it.

## Anti-Patterns

1. **Forgetting to check that probabilities sum to 1.0 at every chance node.** Always validate before computing. If they do not sum to 1.0, flag the error and ask the user to clarify.

2. **Using EMV when the decision-maker is clearly risk-averse.** If payoffs are large relative to the decision-maker's wealth or the user mentions risk concern, switch to expected utility and certainty equivalents.

3. **Confusing EVPI with VOC.** EVPI assumes you learn the state but cannot change it. VOC assumes you can choose the state. VOC >= EVPI always. Do not interchange them.

4. **Applying Bayes' Rule without specifying both the prior and the likelihood.** Both P(A) and P(B|A) must be defined for every state. If the user provides only one, ask for the other before proceeding.

5. **Ignoring the cost of the test when computing value of imperfect information.** The net value equals the gross value of the test minus its cost. A test with positive gross value may still not be worth purchasing.

6. **Treating real options as free.** Flexibility has value, but maintaining optionality often has costs (holding costs, opportunity costs, delayed revenue). Always account for these in the multistage tree.

7. **Building an Excel model with openpyxl or xlsxwriter.** All Excel output must go through Shortcut.ai API via `shortcut_excel.py`. This is mandatory -- never generate .xlsx files with Python libraries directly.

8. **Presenting a decision tree without a clear rollback annotation.** Every intermediate node must show its computed value so the user can trace the logic. A tree with only end-node payoffs is incomplete.

9. **Running sensitivity analysis on irrelevant parameters.** Focus on parameters that the decision-maker has genuine uncertainty about and that plausibly affect the optimal choice. Do not vary parameters that are contractually fixed or precisely known.

10. **Collapsing a multistage decision into a single stage.** If the problem has sequential decisions separated by information revelation, each stage must be a separate decision node in the tree. Collapsing stages destroys the value of flexibility.

## Related Skills

- **Requires:** `probability-distributions` -- for specifying outcome distributions at chance nodes.
- **Feeds into:** `project-valuation` -- decision analysis with real options extends static NPV/DCF valuation.
- **Pairs with:** `monte-carlo-simulation` -- when outcome distributions are continuous or complex, Monte Carlo can estimate EMV and risk profiles that feed into the decision tree.
