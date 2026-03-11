---
name: Classification
description: "Build classification models with logistic regression, decision trees, naive Bayes, neural networks, and ensemble methods. Evaluate with confusion matrices, ROC curves, and AUC. Use: classification, logistic regression, classify, predict class, churn prediction, credit scoring, fraud detection, customer targeting, decision tree classifier, CART, naive Bayes, neural network classifier, confusion matrix, sensitivity, specificity, ROC curve, AUC, accuracy, precision, recall, F1 score, class imbalance, rare events, oversampling, train test split, data partitioning, validation set, holdout, cross-validation, odds ratio, log-odds, sigmoid, cutoff threshold, lift chart, gains table, binary prediction, supervised learning, label prediction, propensity model, response model, scoring model"
---

# Classification

## Purpose

Provide a complete supervised-learning classification toolkit for business problems where the target variable is categorical (typically binary). This skill covers data partitioning, model building (logistic regression, classification trees, naive Bayes, neural networks), model evaluation (confusion matrix, ROC/AUC, lift), and handling of imbalanced classes. All outputs are delivered in Excel (via Shortcut.ai API with IB formatting) or Python (scikit-learn + pandas + matplotlib), or both, depending on the user's request.

## When to Use

- The goal is to predict a binary or multi-class outcome (yes/no, churn/stay, fraud/legitimate, buy/not buy).
- You need to score customers, transactions, or entities by probability of belonging to a class.
- A business rule or threshold-based system is underperforming and a data-driven model could improve accuracy.
- You need to compare multiple classification methods on the same dataset and pick the best one.
- Class imbalance exists (e.g., 2% fraud rate) and naive accuracy is misleading.
- Stakeholders need interpretable outputs: which features drive the prediction and by how much.
- You need to set an optimal cutoff threshold that balances false positives and false negatives for business cost considerations.

## Foundation

### Core Concepts

**Supervised Learning:** The model learns from labeled training data -- each observation has known feature values and a known target class. The trained model then predicts the class for new, unseen observations.

**Data Partitioning:** Split the dataset before any modeling:
- **Training set (typically 60-70%):** Used to fit the model.
- **Validation set (typically 15-20%):** Used to tune hyperparameters and compare models.
- **Test set (typically 15-20%):** Used once at the end for an unbiased performance estimate.

Never evaluate a model on the data it was trained on. This is the single most important rule in predictive modeling.

### Mathematical Framework

**Logistic Regression:**

The probability that observation belongs to class 1:

```
P(Y=1|X) = 1 / (1 + e^(-(b0 + b1*x1 + b2*x2 + ... + bk*xk)))
```

Log-odds (logit) form:

```
ln(P / (1 - P)) = b0 + b1*x1 + b2*x2 + ... + bk*xk
```

Odds ratio interpretation: a one-unit increase in x_i multiplies the odds of Y=1 by e^(b_i). If e^(b_i) = 1.5, the odds increase by 50%.

**Classification Trees (CART):**

Trees split data recursively to maximize class purity. Two common splitting criteria:

Gini impurity (used by CART):

```
Gini(node) = 1 - SUM(p_i^2)   for all classes i
```

Where p_i is the proportion of class i in the node. Gini = 0 means pure node.

Entropy (used by C4.5/ID3):

```
H(node) = -SUM(p_i * log2(p_i))   for all classes i
```

At each step, choose the split that maximizes the reduction in impurity (information gain).

**Naive Bayes:**

Applies Bayes' theorem with the "naive" assumption that features are conditionally independent given the class:

```
P(class|x1, x2, ..., xk) proportional to P(class) * PRODUCT(P(x_i|class))
```

Despite the strong independence assumption, naive Bayes often performs surprisingly well, especially with text data and when training data is limited.

**Neural Networks:**

Multi-layer perceptrons with input layer (features), one or more hidden layers (nonlinear transformations), and output layer (class probabilities via softmax or sigmoid). Trained via backpropagation to minimize cross-entropy loss. Powerful but less interpretable than logistic regression or trees.

### Evaluation Metrics

**Confusion Matrix (for binary classification):**

```
                    Predicted Positive    Predicted Negative
Actual Positive         TP                    FN
Actual Negative         FP                    TN
```

Key metrics derived from the confusion matrix:

```
Accuracy    = (TP + TN) / (TP + TN + FP + FN)
Sensitivity = TP / (TP + FN)          (recall, true positive rate)
Specificity = TN / (TN + FP)          (true negative rate)
Precision   = TP / (TP + FP)          (positive predictive value)
F1 Score    = 2 * Precision * Sensitivity / (Precision + Sensitivity)
```

**ROC Curve and AUC:**

The ROC curve plots Sensitivity (y-axis) vs. 1 - Specificity (x-axis) across all possible cutoff thresholds. AUC (Area Under the Curve) summarizes overall discriminative ability:
- AUC = 0.5: no better than random guessing.
- AUC = 0.7-0.8: acceptable discrimination.
- AUC = 0.8-0.9: good discrimination.
- AUC > 0.9: excellent discrimination.

**Handling Rare Events / Imbalanced Classes:**

When one class is rare (e.g., fraud at 1%), overall accuracy is misleading -- a model predicting "no fraud" for everyone gets 99% accuracy but catches zero fraud. Strategies:
- Oversample the minority class or undersample the majority class.
- Use class weights in the model.
- Evaluate with sensitivity, specificity, and AUC rather than accuracy alone.
- Choose the cutoff threshold based on business costs (cost of false positive vs. false negative).

### Key Inputs

| Input | Description | Default |
|-------|-------------|---------|
| Dataset | Labeled data with features and target variable | Required |
| Target variable | Binary or multi-class column to predict | Required |
| Features | Predictor columns (numeric and/or categorical) | All non-target columns |
| Method | logistic, tree, naive_bayes, neural_net, or compare_all | compare_all |
| Train/test split | Proportion for training vs. test | 70/30 |
| Cutoff threshold | Probability threshold for positive class | 0.5 |
| Class weights | Adjustment for imbalanced classes | None (balanced if rare events detected) |

## Process

### Entry Mode Detection

Detect the user's entry mode from their prompt:

**Mode 1 -- Guided (user says "help me classify," "which model should I use," or provides incomplete info):**
1. Ask for: (a) the dataset or description, (b) the target variable, (c) candidate features, (d) business context (what is the cost of false positives vs. false negatives?), (e) whether class imbalance is a concern.
2. Recommend a method or compare multiple methods.
3. Present results with business interpretation.

**Mode 2 -- Context Dump (user provides a full dataset, case description, or homework problem):**
1. Parse the dataset, target, features, and any specified methods from the provided text.
2. Identify the appropriate classification approach(es).
3. Build the complete analysis and present results.

**Mode 3 -- Quick Draft (user gives a direct command like "run logistic regression on this data" or "build a classification tree"):**
1. Execute the specific model requested.
2. Deliver output immediately with minimal preamble.

### Output Mode Detection

Determine the output format from the user's prompt:

- **"Excel" / "spreadsheet" / "model"** --> Excel mode via Shortcut.ai API.
- **"Python" / "code" / "script" / "run" / "compute"** --> Python mode with scikit-learn + pandas + matplotlib.
- **"Both"** --> Deliver both Excel and Python outputs.
- **"Teach" / "explain" / "how does" / "walk me through"** --> Teaching mode: show derivations, annotate each step, no file output unless requested.
- **No explicit mode** --> Default to Python (classification is inherently computational; ask user if Excel is also desired).

### Analysis Workflow

1. **Data preparation:** Load the dataset, identify target and features, handle missing values, encode categorical variables (one-hot or label encoding as appropriate), check for class imbalance.
2. **Partition data:** Split into training and test sets (default 70/30). If user requests, use k-fold cross-validation instead.
3. **Build model(s):** Fit the specified method(s) on the training set. If compare_all, fit logistic regression, classification tree, naive Bayes, and neural network.
4. **Evaluate on test set:** Generate confusion matrix, compute accuracy, sensitivity, specificity, precision, F1, and AUC for each model.
5. **ROC analysis:** Plot ROC curves for all models on the same chart. Identify the best model by AUC.
6. **Feature importance:** For logistic regression, report coefficients and odds ratios. For trees, report feature importance scores. For neural nets, note that direct interpretation is limited.
7. **Cutoff optimization:** If business costs are specified, find the cutoff threshold that minimizes total expected cost (or maximizes net benefit).
8. **Final recommendation:** State which model to deploy and why, with the expected performance on new data.

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
- Thin bottom borders between sections; double bottom border above totals.
- Column A: row labels, left-aligned. Data columns: right-aligned.
- Gridlines off, print area set, freeze panes on headers.

**Tab Structure:**

| Tab | Contents |
|-----|----------|
| Model Coefficients | For logistic regression: variable names, coefficients, standard errors, p-values, odds ratios. For trees: split rules and feature importance. For all methods: a summary comparison row with AUC. |
| Confusion Matrix | One confusion matrix per model evaluated. Clearly labeled with TP, FP, TN, FN counts. Below each matrix: accuracy, sensitivity, specificity, precision, F1. |
| Accuracy Metrics | Side-by-side comparison table: each row is a metric (accuracy, sensitivity, specificity, precision, F1, AUC), each column is a model. Best value in each row highlighted. |
| ROC Data | Columns for each model: false positive rate and true positive rate at various thresholds. AUC noted at top. Data structured for charting. |
| Assumptions | Train/test split ratio, features used, encoding method, class balance handling, cutoff threshold, any data exclusions. |

## Python Output Specification

**Libraries:** scikit-learn, pandas, matplotlib, numpy.

**Outputs:**

1. **Model fitting and evaluation:** Train specified model(s), print classification report and confusion matrix to console.

2. **ROC curve plot (matplotlib):**
   - One ROC curve per model, each in a distinct color.
   - Diagonal reference line (random classifier).
   - AUC value in the legend for each model.
   - Title, axis labels, clean layout.

3. **Feature importance / coefficients plot (matplotlib):**
   - Horizontal bar chart of top features by importance or absolute coefficient value.
   - Labeled axes, sorted by magnitude.

4. **Confusion matrix heatmap (matplotlib):**
   - Color-coded matrix with counts and percentages annotated in each cell.

**Code standards:**
- scikit-learn for all model fitting and evaluation.
- pandas for data manipulation.
- matplotlib for all visualizations.
- Functions are modular: `prepare_data()`, `train_model()`, `evaluate_model()`, `plot_roc()`, `plot_importance()`.
- All parameters defined at the top of the script for easy modification.
- Comments explaining each computation step.
- Use `python` (not `python3`).

## Output

Regardless of mode, every classification analysis must include:

1. **Model performance summary:** Table comparing all evaluated models on key metrics (accuracy, sensitivity, specificity, AUC).
2. **Best model recommendation:** Which model to use and why, stated in one sentence.
3. **Confusion matrix:** For at least the best model, showing TP/FP/TN/FN on the test set.
4. **ROC/AUC:** Curve or AUC value demonstrating discriminative power.
5. **Key drivers:** Top 3-5 features that most influence the prediction, with direction of effect where interpretable.
6. **Cutoff guidance:** The recommended probability threshold and its rationale (default 0.5 unless business costs dictate otherwise).
7. **Imbalance note:** If class imbalance exists, explicitly state how it was handled and why accuracy alone is insufficient.
8. **Business interpretation:** Translate model outputs into actionable business language (e.g., "customers with 3+ service calls and contract month-to-month are 4x more likely to churn").

## Anti-Patterns

1. **Evaluating on the training set.** Never report training accuracy as if it represents real-world performance. Always evaluate on held-out test data. Training accuracy is almost always optimistically biased.

2. **Using accuracy as the sole metric with imbalanced classes.** A 99% accuracy on a dataset with 1% positive rate is meaningless. Always report sensitivity, specificity, and AUC alongside accuracy.

3. **Forgetting to encode categorical variables.** Scikit-learn models (except tree-based in some implementations) require numeric inputs. Failing to one-hot encode or label-encode categoricals will cause errors or garbage results.

4. **Overfitting the classification tree.** Unpruned trees memorize the training data. Always set max_depth, min_samples_leaf, or use cost-complexity pruning. Compare pruned vs. unpruned performance on the validation set.

5. **Ignoring the cutoff threshold.** The default 0.5 cutoff is arbitrary. In fraud detection you might lower it to 0.1 to catch more fraud (accepting more false positives). In medical screening the cost of a miss may be life-threatening. Always tie the cutoff to business costs.

6. **Treating naive Bayes probability outputs as well-calibrated.** Naive Bayes often produces extreme probabilities (near 0 or 1) due to the independence assumption. Use the class predictions but interpret the raw probabilities cautiously.

7. **Building an Excel model with openpyxl or xlsxwriter.** All Excel output must go through Shortcut.ai API via `shortcut_excel.py`. This is mandatory -- never generate .xlsx files with Python libraries directly.

8. **Skipping data exploration before modeling.** Always check for missing values, outliers, multicollinearity (for logistic regression), and class distribution before fitting any model. Garbage in, garbage out.

9. **Comparing models trained on different data splits.** When comparing logistic regression vs. tree vs. naive Bayes, all must be trained on the same training set and evaluated on the same test set. Use the same random seed for partitioning.

10. **Reporting feature importance without context.** A variable may be statistically important but not actionable. Always pair importance scores with business interpretation -- can the business actually use this feature for decisions?

## Related Skills

- **Pairs with:** `clustering-market-basket` -- use clustering for segmentation (unsupervised), then build classification models within each segment, or use cluster membership as a feature in classification.
- **Feeds into:** Decision support workflows where predicted probabilities drive targeting, pricing, or risk management actions.
- **Requires:** Clean, labeled data. If the target variable is not available, consider `clustering-market-basket` for unsupervised exploration first.
