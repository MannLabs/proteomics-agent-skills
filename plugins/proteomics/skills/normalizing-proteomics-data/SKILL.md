---
name: normalizing-proteomics-data
description: Evaluate the need for and perform normalization of protein-level proteomics intensity data. Use when (1) assessing whether normalization is needed, (2) selecting normalization methods, (3) applying it. Does NOT cover batch correction or imputation
---

# Normalizing Proteomics Data

Normalization makes samples comparable by aligning their overall intensity distributions. It serves three main goals:

- Aligning intensities across samples, which removes technical variation caused by differences in sample loading amounts or instrument behavior.
- Preserving feature ranks within each sample, so the relative ordering of features is not distorted.
- Stabilizing variance (i.e., addressing heteroscedasticity), which most downstream methods require because they assume homoscedastic noise.

After normalization, a feature's intensity should reflect its relative biological abundance within a sample and remain comparable across samples.

Common normalization methods include:

| Priority | Method                  | Input scale |
| --- | --- | --- | --- |
| 1        | Total sum normalization | Simple offset correction; robust baseline                                            | Linear-scale intensities                |
| 2        | Quantile Normalization                | Distributions should be identical across samples                                     | Log-transformed intensities  |
| 3        | Variance Stabilizing Normalization (VSN)                     | Need variance stabilization of low-abundant features; performs well in differential expression benchmarks | Linear-scale intensities              |
| 4        | LOESS/RLR               | Suspect intensity-dependent bias (non-linear or linear)                              | Log-transformed intensities  |

**Recommendation**: Start with log2 only. If PCA shows intensity-driven structure, try total sum normalization, then VSN.

## Checklist

Copy this checklist and track progress:

```
Analysis step progress:
- [ ] Verify log2 transformation
- [ ] Assess Normalization Need
- [ ] Data dependent: Select Additional Normalization
- [ ] Evaluate Normalization Effect
      If Successful: Proceed to "Batch correction"
      If Unsuccessful: Return to Step 3 (Select normalization method 2, 3, etc.)
```

## Workflow

### 1. Apply Log Transformation (Always Recommended)

Handle zeros as missing before log transformation

### 2. Assess Normalization Need

Check if total summed intensity drives variation:

- Visualize PCA colored by total intensity
- Compute correlation between PC1 and log10(total intensity per sample)
- If total intensity correlates strongly with major PCs, perform normalization attempts

### 3. Select Additional Normalization (if necessary)

**When total intensity drives variance**, try the normalization methods from the normalization method table in the order of their priority.

> [!Important] Different normalization methods assume different data distributions. Ensure _a priori_, that the current data distribution meets the assumptions of the normalization method based on the column `Input scale` in the table. If you select a normalization method that assumes linear-scaled intensities, you need to go back to linear-scaled intensities and apply these normalization methods to the raw intensities, then log-transform the normalized data.

### 4. Evaluate Normalization Effect (Required)

It is critical that you assess the performance of the normalization, as benchmarks showed that many normalization methods perform worse than log2 alone on spike-in datasets and LFQ-normalized data (e.g., directLFQ) may not require additional normalization.

**Pooled Median Absolute Deviation (PMAD)** Average median absolute deviation across all features within a biological condition should drop

- **Correlation of principal components with total intensity** should drop.

- **Pearson correlation between technical replicates** should increase or stay stable
