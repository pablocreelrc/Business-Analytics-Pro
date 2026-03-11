---
name: Clustering and Market Basket Analysis
description: "Build clustering models with K-means, hierarchical clustering, and run market basket analysis with association rules. Use: clustering, K-means, cluster analysis, customer segmentation, segment customers, hierarchical clustering, dendrogram, market basket analysis, association rules, Apriori, cross-selling, product affinity, bundle products, support confidence lift, elbow method, silhouette score, distance measure, Euclidean distance, Manhattan distance, standardize variables, cluster profiling, agglomerative clustering, Ward linkage, complete linkage, single linkage, unsupervised learning, group similar, find segments, natural groupings, itemset mining, frequent itemsets, basket analysis, purchase patterns, recommend products, co-purchase, affinity analysis"
---

# Clustering and Market Basket Analysis

## Purpose

Provide a complete unsupervised-learning toolkit for two related but distinct tasks: (1) clustering -- discovering natural groupings in data without predefined labels, and (2) market basket analysis -- finding association rules that reveal which items are frequently purchased together. This skill covers K-means clustering, hierarchical clustering, distance measures, cluster evaluation, the Apriori algorithm, and association rule metrics (support, confidence, lift). All outputs are delivered in Excel (via Shortcut.ai API with IB formatting) or Python (scikit-learn + mlxtend + pandas + matplotlib), or both, depending on the user's request.

## When to Use

- You need to segment customers, products, stores, or any entities into meaningful groups without predefined labels.
- Marketing wants to identify distinct customer profiles for targeted campaigns.
- You need to decide how many natural segments exist in the data.
- Transaction data is available and you want to find products frequently bought together for cross-selling, bundling, or store layout optimization.
- You need to generate "customers who bought X also bought Y" recommendations.
- A business stakeholder asks for data-driven groupings rather than ad hoc segments based on intuition.
- You want to reduce a large, heterogeneous population into a manageable number of actionable segments.

## Foundation

### Clustering Concepts

**Unsupervised Learning:** Unlike classification, there is no target variable. The algorithm discovers structure in the data based on similarity among observations.

**Distance Measures:**

Euclidean distance (most common):

```
d(x, y) = sqrt(SUM((x_i - y_i)^2))   for all features i
```

Manhattan distance (sum of absolute differences):

```
d(x, y) = SUM(|x_i - y_i|)   for all features i
```

**Critical: Standardization.** Features measured on different scales (e.g., income in thousands vs. age in decades) will distort distance calculations. Always standardize (z-score: subtract mean, divide by standard deviation) before clustering unless all features share the same unit and scale.

### K-Means Clustering

**Algorithm:**
1. Choose K (number of clusters).
2. Randomly initialize K cluster centroids.
3. Assign each observation to the nearest centroid.
4. Recompute centroids as the mean of all assigned observations.
5. Repeat steps 3-4 until assignments stabilize (convergence).

**Objective function:** Minimize the total within-cluster sum of squares (WCSS):

```
WCSS = SUM over all clusters k [ SUM over observations in k [ d(x, centroid_k)^2 ] ]
```

**Choosing K:**

Elbow method: Plot WCSS against K (from 1 to some max). Look for the "elbow" where adding another cluster yields diminishing improvement.

Silhouette score: For each observation i, measure how well it fits its own cluster vs. the nearest neighboring cluster:

```
s(i) = (b(i) - a(i)) / max(a(i), b(i))
```

Where:
- a(i) = average distance from i to all other points in its own cluster.
- b(i) = average distance from i to all points in the nearest other cluster.
- s(i) ranges from -1 (wrong cluster) to +1 (well clustered).

Average silhouette score across all observations summarizes cluster quality. Higher is better.

### Hierarchical Clustering

**Agglomerative (bottom-up) algorithm:**
1. Start with each observation as its own cluster.
2. Merge the two closest clusters.
3. Repeat until all observations are in one cluster.
4. Cut the dendrogram at the desired level to get K clusters.

**Linkage methods (how to measure distance between clusters):**
- **Single linkage:** Minimum distance between any pair across clusters. Tends to produce elongated chains.
- **Complete linkage:** Maximum distance between any pair. Produces compact, spherical clusters.
- **Ward's method:** Minimizes the increase in total within-cluster variance at each merge. Often the best default.

**Dendrogram:** A tree diagram showing the merge history. Horizontal cuts at different heights yield different numbers of clusters. Long vertical lines suggest natural cluster boundaries.

### Market Basket Analysis

**Association Rules:** Rules of the form {A} --> {B}, meaning "if a customer buys A, they tend to also buy B."

**Key metrics:**

Support -- how frequently the itemset appears in all transactions:

```
Support(A and B) = P(A and B) = (# transactions with both A and B) / (# total transactions)
```

Confidence -- how often B appears in transactions that contain A:

```
Confidence(A --> B) = P(B|A) = P(A and B) / P(A)
```

Lift -- how much more likely B is given A, compared to B's baseline rate:

```
Lift(A --> B) = P(B|A) / P(B) = Confidence(A --> B) / Support(B)
```

Interpretation:
- Lift > 1: A and B appear together more than expected (positive association).
- Lift = 1: A and B are independent.
- Lift < 1: A and B appear together less than expected (negative association).

**Apriori Algorithm:**

The Apriori principle: if an itemset is infrequent (below minimum support), all its supersets are also infrequent. This prunes the search space dramatically.

Algorithm:
1. Find all itemsets with support >= minimum support threshold.
2. Start with individual items, then extend to pairs, triples, etc.
3. At each level, discard itemsets whose subsets are infrequent (Apriori pruning).
4. Generate rules from frequent itemsets that meet minimum confidence.

### Key Inputs

| Input | Description | Default |
|-------|-------------|---------|
| Dataset | Observations with numeric features (clustering) or transaction records (basket) | Required |
| K | Number of clusters (or auto-select via elbow/silhouette) | Auto-select |
| Distance metric | Euclidean or Manhattan | Euclidean |
| Linkage method | Ward, complete, single, average (for hierarchical) | Ward |
| Min support | Minimum support threshold for Apriori | 0.01 |
| Min confidence | Minimum confidence threshold for rules | 0.5 |
| Min lift | Minimum lift to report a rule | 1.0 |

## Process

### Entry Mode Detection

Detect the user's entry mode from their prompt:

**Mode 1 -- Guided (user says "help me segment," "how many clusters," or provides incomplete info):**
1. Ask for: (a) the dataset or description, (b) which variables to cluster on, (c) whether they have a sense of how many groups exist, (d) for basket analysis: the transaction format and any minimum thresholds.
2. Recommend an approach (K-means vs. hierarchical, or both).
3. Present results with business interpretation of each cluster or top rules.

**Mode 2 -- Context Dump (user provides a full dataset, case description, or homework problem):**
1. Parse the dataset, features, and any specified parameters from the provided text.
2. Determine whether the task is clustering, market basket, or both.
3. Build the complete analysis and present results.

**Mode 3 -- Quick Draft (user gives a direct command like "run K-means with 4 clusters" or "find association rules with min support 0.05"):**
1. Execute the specific analysis requested.
2. Deliver output immediately with minimal preamble.

### Output Mode Detection

Determine the output format from the user's prompt:

- **"Excel" / "spreadsheet" / "model"** --> Excel mode via Shortcut.ai API.
- **"Python" / "code" / "script" / "run" / "compute"** --> Python mode with scikit-learn + mlxtend + pandas + matplotlib.
- **"Both"** --> Deliver both Excel and Python outputs.
- **"Teach" / "explain" / "how does" / "walk me through"** --> Teaching mode: show derivations, annotate each step, no file output unless requested.
- **No explicit mode** --> Default to Python (clustering and basket analysis are inherently computational; ask user if Excel is also desired).

### Clustering Workflow

1. **Data preparation:** Load dataset, select clustering features, handle missing values, standardize all numeric features (z-score).
2. **Choose K:** Run K-means for K = 2 through K = 10 (or user-specified range). Plot the elbow curve (WCSS vs. K) and compute average silhouette scores. Recommend K based on elbow + silhouette.
3. **Fit final K-means:** Run K-means with the chosen K. Record cluster assignments and centroids.
4. **Hierarchical clustering (if requested or for comparison):** Build the agglomerative hierarchy with Ward linkage, plot the dendrogram, cut at the chosen K.
5. **Profile clusters:** For each cluster, compute the mean (and standard deviation) of each feature. Identify the distinguishing characteristics: which features are notably above or below the overall mean.
6. **Visualize:** Scatter plot of the two most important features (or PCA-reduced 2D), colored by cluster. Centroid markers overlaid.
7. **Business interpretation:** Name each cluster in plain language (e.g., "High-value loyalists," "Price-sensitive churners," "New low-engagement").

### Market Basket Workflow

1. **Data preparation:** Convert transaction data to a binary item-by-transaction matrix (1 if item present, 0 otherwise).
2. **Run Apriori:** Find frequent itemsets meeting minimum support.
3. **Generate rules:** From frequent itemsets, generate rules meeting minimum confidence and lift thresholds.
4. **Rank rules:** Sort by lift (descending) to surface the most interesting non-obvious associations.
5. **Filter and interpret:** Remove trivially obvious rules. Highlight rules with high lift and sufficient support for actionability.
6. **Business recommendations:** Translate top rules into cross-selling strategies, bundle suggestions, or layout changes.

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
| Cluster Assignments | Each observation with its cluster label. Columns: observation ID, original features (standardized values), assigned cluster. Sorted by cluster. |
| Cluster Profiles | Summary table: one row per cluster, columns for cluster size (n and %), mean of each feature, and a plain-language cluster name. Distinguishing features highlighted. |
| Association Rules Table | Columns: antecedent itemset, consequent itemset, support, confidence, lift. Sorted by lift descending. Only rules meeting all thresholds included. |
| Assumptions | Clustering: features used, standardization method, K chosen and rationale, distance metric, linkage method. Basket: min support, min confidence, min lift, number of transactions, number of unique items. |

## Python Output Specification

**Libraries:** scikit-learn, mlxtend (for Apriori), pandas, matplotlib, numpy.

**Outputs:**

1. **Elbow plot (matplotlib):** WCSS vs. K with a vertical dashed line at the recommended K.

2. **Silhouette plot (matplotlib):** Average silhouette score vs. K, or silhouette diagram for the chosen K.

3. **Cluster scatter plot (matplotlib):** 2D scatter (top 2 features or PCA components), colored by cluster, centroids marked with X.

4. **Dendrogram (matplotlib):** If hierarchical clustering is used, a dendrogram with a horizontal cut line at the chosen K.

5. **Cluster profile table:** Printed to console -- mean of each feature by cluster with interpretation.

6. **Association rules table:** Printed to console or as a pandas DataFrame -- antecedent, consequent, support, confidence, lift.

**Code standards:**
- scikit-learn for K-means and hierarchical clustering.
- mlxtend for Apriori and association rules.
- pandas for data manipulation.
- matplotlib for all visualizations.
- Functions are modular: `prepare_data()`, `find_optimal_k()`, `fit_clusters()`, `profile_clusters()`, `run_apriori()`, `plot_elbow()`, `plot_clusters()`.
- All parameters defined at the top of the script for easy modification.
- Comments explaining each computation step.
- Use `python` (not `python3`).

## Output

Regardless of mode, every analysis must include:

**For Clustering:**
1. **Recommended K:** The number of clusters with rationale (elbow, silhouette, or domain logic).
2. **Cluster profiles:** A summary table with mean feature values per cluster and plain-language cluster names.
3. **Cluster sizes:** Count and percentage of observations in each cluster.
4. **Visualization:** Scatter plot or dendrogram showing the cluster structure.
5. **Business interpretation:** What each cluster represents and how a business could act on the segmentation (e.g., different marketing strategies per segment).

**For Market Basket Analysis:**
1. **Top association rules:** Ranked by lift, with support and confidence alongside.
2. **Actionable recommendations:** Specific cross-selling or bundling strategies derived from the top rules.
3. **Non-obvious findings:** Highlight rules that are surprising (high lift but not intuitively expected).
4. **Threshold justification:** Why the chosen support/confidence/lift thresholds are appropriate for this dataset.

## Anti-Patterns

1. **Clustering without standardizing variables.** If income is in thousands and age is in decades, distance calculations will be dominated by income. Always z-score standardize before computing distances.

2. **Choosing K based only on the elbow method.** The elbow is often ambiguous. Always supplement with silhouette scores and domain knowledge. If the business naturally has 3 customer tiers, K=3 may be right even if the elbow suggests K=4.

3. **Interpreting clusters as causal segments.** Clustering finds correlational groupings, not causal relationships. "Cluster 2 has high churn" does not mean being in Cluster 2 causes churn. It means these customers share features associated with churn.

4. **Using association rules with too-low support thresholds.** Rules with 0.1% support may be statistically unreliable even if lift is high. Ensure sufficient transaction volume backs each rule before making business recommendations.

5. **Ignoring lift and relying only on confidence.** High confidence can be misleading if the consequent item is already very popular. A rule "bread --> milk" with 80% confidence is uninteresting if 75% of all baskets contain milk (lift near 1.0). Always check lift.

6. **Treating K-means results as stable without multiple runs.** K-means depends on random initialization and can converge to local optima. Always run with multiple random seeds (n_init >= 10 in scikit-learn) and select the run with the lowest WCSS.

7. **Building an Excel model with openpyxl or xlsxwriter.** All Excel output must go through Shortcut.ai API via `shortcut_excel.py`. This is mandatory -- never generate .xlsx files with Python libraries directly.

8. **Including non-numeric or binary ID columns in clustering.** Customer IDs, names, and other identifiers must be excluded from the feature set. Including them adds noise and distorts distances.

9. **Reporting raw centroids without back-transforming to original scale.** Stakeholders cannot interpret z-scored centroid values. Always present cluster profiles in the original units (mean income = $85,000, not mean z-score = 1.3).

10. **Generating hundreds of association rules without filtering.** An unfiltered rule list is unusable. Rank by lift, filter by minimum support and confidence, and present only the top 10-20 actionable rules.

## Related Skills

- **Pairs with:** `classification` -- use clustering to create segments, then build classification models to predict segment membership for new customers, or use cluster labels as features in a classification model.
- **Feeds into:** Marketing strategy, CRM targeting, store layout optimization, recommendation engines.
- **Requires:** Clean numeric data for clustering. Transaction-level data for market basket analysis.
