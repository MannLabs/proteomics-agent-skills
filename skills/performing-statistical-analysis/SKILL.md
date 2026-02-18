---
name: performing-statistical-analysis
description: Perform differential expression analysis on preprocessed proteomics data. Use this when performing finding testing (t-tests, ANOVA) to identify regulated proteins, and multiple testing correction (FDR). Generates standard visualizations like Volcano plots.
---

# Performing Proteomics Statistical Analysis
The goal of Differential Expression Analysis (DEA) is to identify proteins that change significantly between conditions.

## Context

### Experimental conditions
* **Pairwise (2 Groups):** Explicitly define "Control" vs. "Treatment"
* **Multi-Group (>2 Groups):**
    * *Reference-based:* Compare every condition against a universal control (e.g., `Drug_A vs Ctrl`, `Drug_B vs Ctrl`).
    * *All-vs-All:* Compare every permutation (e.g., `Drug_A vs Drug_B`).
    * *Global:* Use ANOVA to detect if *any* change exists across groups.

### Statistical models
* **t-test**: The default for pairwise comparisons. With small sample sizes (typically n < 5 per group), use moderated t-tests (e.g., limma's eBayes), which borrow variance information across proteins to stabilize estimates.
* **ANOVA:** Required when comparing more than two groups simultaneously.
* **Linear models:** Recommended for complex designs. Use standard linear models for factorial (case-control) designs or batch correction; use linear mixed-effects models specifically when you have repeated measures (e.g., paired samples, time-courses) to account for within-subject correlation.

### Multiple Testing Correction
Correcting the resulting p-values for multiple finding testing. The standard correction method to control false positives identifications is Benjamini-Hochberg FDR.

## 2. Workflow Statistical Testing

Copy this checklist and track progress:

```
Testcase Progress:
- [ ] Step 1: Define the experimental conditions for comparision
- [ ] Step 2: Run statistical model
- [ ] Step 3: Apply Multiple Testing Correction
- [ ] Step 4: Filter for regulated proteins
- [ ] Step 5: Create a Volcano plot
- [ ] Step 6: Create a Heatmap
- [ ] Step 7: Export results
```

### Step 1: Define the experimental conditions for comparision
Check the study context (`Experiment.md`) and the prompt.

- For 2 Groups: Define group1 (Control) and group2 (Treatment).
- For >2 Groups: Select Strategy A (ANOVA for global differences) or Strategy B (Specific Pairwise Contrasts).

### Step 2: Run a statistical model
- Pairwise: t-test. Input: Log2-transformed intensities.
- Multi-group global: One-way ANOVA.
Output: Calculate log2_fold_change and raw p_value.

### Step 3: Apply Multiple Testing Correction
Method: Apply Benjamini-Hochberg (BH) to raw p-values.
Result: Generate a q_value (FDR-adjusted p-value) column.

### Step 4: Filter for regulated proteins
Mark proteins as "Significant" (True/False) based on:
- q_value < 0.05 (5% FDR).
- abs(log2_fold_change) > 1 (2-fold change).

### Step 5: Create a Volcano plot
Scatter plot of log2_fold_change (x-axis) vs. -Log10 q-value (y-axis). Color significant points.

Hints - Too many significant hits: The cutoff is too loose. Increase log2_fold_change threshold or use a stricter FDR (0.01).

### Step 6: Create a Heatmap
- Data: Subset to "Significant" proteins.
- Scaling: Z-score normalize per feature (here: protein).
- Clustering: Hierarchical clustering on rows=samples and columns=proteins. Validates if replicates cluster together.

### Step 7: Export results
Save table `results_statistical_analysis.tsv` with columns: `protein_group`, `gene_name`, `log2_fold_change`, `p_value`, `-log10_pvalue`, `q_value`, `is_significant`.
