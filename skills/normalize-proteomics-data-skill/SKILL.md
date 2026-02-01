---
name: normalizing-proteomics-data
description: Evaluate the need for and perform normalization of protein-level proteomics intensity data. Use when (1) assessing whether normalization is needed before downstream analysis, (2) selecting appropriate normalization methods, (3) transforming proteomics expression matrices to make samples comparable, or (4) stabilizing variance for PCA or differential expression analysis. Does NOT cover batch correction or imputation.
---

# Proteomics Data Normalization

Normalization makes samples comparable by aligning sample-wide intensity distributions, removing technical variation from varying sample amounts or instrument differences. After normalization, feature intensity reflects relative biological abundance.

## Checklist

- [ ] Apply log2 transformation
- [ ] Assess Normalization Need
- [ ] Data dependent: Select Additional Normalization
- [ ] Evaluate Normalization Effect

## Workflow

### 1. Apply Log Transformation (Always Recommended)

Always log2-transform intensities before other normalization. This:

- Stabilizes variance (addresses heteroscedasticity)
- Is required for most downstream methods assuming homoskedastic noise

Handle zeros as missing before log transformation

### 2. Assess Normalization Need

Check if total summed intensity drives variation:

- Visualize PCA colored by total intensity
- Compute correlation between PC1 and log10(total intensity per sample)
- If total intensity correlates strongly with major PCs, normalization may help

### 3. Select Additional Normalization (if necessary)

**When total intensity drives variance**, choose from:

| Method           | Use When                                                    |
| ---------------- | ----------------------------------------------------------- |
| Median centering | Simple offset correction; robust baseline                   |
| Mean centering   | Samples differ by constant offset                           |
| Quantile         | Distributions should be identical across samples            |
| VSN              | Need variance stabilization; performs well in DE benchmarks |
| LOESS/RLR        | Suspect intensity-dependent bias (non-linear or linear)     |

**Recommendation**: Start with log2 only. If PCA shows intensity-driven structure, try median centering or VSN.

![Important] VSN normalization expects raw intensities - if you choose VSN, you need to go back to raw intensities and apply VSN to the raw intensities.

### 4. Evaluate Normalization Effect (Required)

It is critical that you assess the performance of the normalization, as benchmarks showed that many normalization methods perform worse than log2 alone on spike-in datasets and LFQ-normalized data (e.g., directLFQ) may not require additional normalization.

**Pooled Median Absolute Deviation (PMAD)**: Average MAD across features (f in F) within biological groups (g). If the normalization works, a decrease is expected.
**Correlation of principal components with total intensity** The correlation should drop.

**Additional checks**:

- PCA before/after: variance should no longer correlate with total intensity
- Pearson correlation between technical replicates should increase or stay stable
