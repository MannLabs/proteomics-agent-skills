---
name: correcting-proteomics-batch-effects
description: Evaluate and correct batch effects in proteomics data. Use when (1) assessing if batch effect, technical variation, plate effects, or instrument drift in proteomics context is present and batch correction is needed, (2) applying batch correction algorithms, (3) validating batch correction results.
---

# Batch Correction for Proteomics Data

Batch effects are systematic technical biases from sample preparation, instruments, or time points that obscure biological signals. Correction should only be applied on the protein level when diagnostics indicate necessity.

Commonly used batch effect correction algorithms include:

| Priority | Algorithm        | Description                          | When to Use                                |
| -------- | ---------------- | ------------------------------------ | ------------------------------------------ |
| 1        | ComBat           | Empirical Bayes shift+scale model    | Default choice, handles small batches well |
| 2        | Median centering | Equalizes batch-wise feature medians | Simple cases, interpretable                |
| 3        | Harmony          | Iterative mixing in PCA space        | Large datasets, many batches               |

## Workflow

Copy this checklist and track progress:

```
Analysis step progress:
- [ ] **Assess batch effects**
- [ ] **Prepare dataset**
- [ ] **Apply correction** - Use appropriate algorithm at protein level
- [ ] **Validate results** - Confirm improvement with same metrics
      If successful: Proceed with statistical analysis
      If unsuccessful: Try another batch effect correction
```

## Details

### Assess Batch Effects

#### Qualitative: Principal Component Analysis (PCA) Visualization

Compute PCA and color samples by technical factors (e.g. plate, instrument, timepoint, processing date). Compare separation by technical vs biological factors. Strong batch effects show samples clustering by technical factors more than biological conditions.

#### Quantitative Metrics

| Metric                               | Description                                               | Interpretation                                                                                                                    |
| ------------------------------------ | --------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Principal Component Regression (PCR) | Weighted correlation of batch covariate with PCs          | `0 - +1`, lower is better                                                                                                         |
| Average Silhouette Width             | Batch clustering in feature/PCA space                     | `-1 - +1`, closer to 0 indicates lower batch effects, closer to 1 for biological factors indicates stronger separation by biology |
| Technical Replicate Correlation      | Pairwise feature correlation of replicates across batches | `-1 - +1`, higher is better                                                                                                       |

**Decision**: Proceed with batch correction if metrics indicate substantial batch effects

- Batch explains at least 5% cumulative explained variance in PCR
- Average Silhouette Width of biological conditions is smaller than Average Silhouette Width
- Pearson correlation of technical replicates is `r<0.9`

### 2. Prepare data

Ensure that

- [ ] Data is log transformed and normalized
- [ ] Batch must not confounded with biological conditions of interest
- [ ] Missing values handled (impute or use NaN-tolerant batch correction method method)

### 3. Apply Batch Correction

Apply batch correction algorithms according to their indicated priorities in the table.

## 4. Validate Correction

Re-compute the same metrics used in assessment:

- PCR score should decrease
- Silhouette width should approach 0
- Technical replicate correlation should increase

If metrics do not improve, repeat step 3 with a different algorithm and investigate which variable confounds the measurement (step 1).
