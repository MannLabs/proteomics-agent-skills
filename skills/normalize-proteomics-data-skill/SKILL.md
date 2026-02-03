---
name: normalizing-proteomics-data
description: Evaluate the need for and perform normalization of protein-level proteomics intensity data. Use when (1) assessing whether normalization is needed, (2) selecting normalization methods, (3) applying it. Does NOT cover batch correction or imputation
---

# Proteomics Data Normalization

Normalization makes samples comparable by aligning sample-wide intensity distributions, removing technical variation from varying sample amounts or instrument differences. Normalization stabilizes variance (addresses heteroscedasticity) which is required for most downstream methods assuming homoskedastic noise. After normalization, feature intensity should reflect their relative biological abundance in the sample.

Common normalization methods include:

| Priority | Method           | Use When                                                                             |
| -------- | ---------------- | ------------------------------------------------------------------------------------ |
| 1        | Median centering | Simple offset correction; robust baseline                                            |
| 2        | Quantile         | Distributions should be identical across samples                                     |
| 3        | VSN              | Need variance stabilization of low-abundant features; performs well in DE benchmarks |
| 4        | LOESS/RLR        | Suspect intensity-dependent bias (non-linear or linear)                              |

**Recommendation**: Start with log2 only. If PCA shows intensity-driven structure, try median centering first, then VSN.

## Checklist

Copy this checklist and track progress:

```
Analysis step progress:
- [ ] Apply log2 transformation
- [ ] Assess Normalization Need
- [ ] Data dependent: Select Additional Normalization
- [ ] Evaluate Normalization Effect
      If Successful: Proceed to "Batch correction"
      If Unsuccessful: Return to Step 3 (Select normalization method 2, 3, etc.)
```

## Workflow

### 1. Apply Log Transformation (Always Recommended)

Handle zeros as missing before log transformation

Always log2-transform intensities before other normalization.

### 2. Assess Normalization Need

Check if total summed intensity drives variation:

- Visualize PCA colored by total intensity
- Compute correlation between PC1 and log10(total intensity per sample)
- If total intensity correlates strongly with major PCs, perform normalization attempts

### 3. Select Additional Normalization (if necessary)

**When total intensity drives variance**, try the following normalization methods from the normalization method table in the order of their priority.

> [!Important] VSN normalization expects raw intensities - if you choose VSN, you need to go back to raw intensities and apply VSN to the raw intensities.

### 4. Evaluate Normalization Effect (Required)

It is critical that you assess the performance of the normalization, as benchmarks showed that many normalization methods perform worse than log2 alone on spike-in datasets and LFQ-normalized data (e.g., directLFQ) may not require additional normalization.

**Pooled Median Absolute Deviation (PMAD)** Average median absolute deviation across all features within a biological condition should drop

- **Correlation of principal components with total intensity** should drop.

- **Pearson correlation between technical replicates** should increase or stay stable
