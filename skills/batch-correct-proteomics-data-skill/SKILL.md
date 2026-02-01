---
name: correcting-proteomics-batch-effects
description: Evaluate and correct batch effects in proteomics data. Use when (1) assessing whether batch correction is needed via PCA visualization or metrics, (2) applying batch correction algorithms like ComBat or median centering, (3) validating batch correction results. Triggers on batch effect, batch correction, technical variation, plate effects, or instrument drift in proteomics context.
---

# Batch Correction for Proteomics Data

Batch effects are systematic technical biases from sample preparation, instruments, time points, or processing plates that obscure biological signals. Correction should only be applied on the protein level when diagnostics indicate necessity.

## Workflow

- [ ] **Assess batch effects** Visualize and quantify before deciding to correct
- [ ] **Ensure that dataset is complete** Ensure that no values are missing or were imputed
- [ ] **Apply correction** - Use appropriate algorithm at protein level
- [ ] **Validate results** - Confirm improvement with same metrics

## Details

### Assess Batch Effects

#### Qualitative: PCA Visualization

Compute PCA and color samples by technical factors (plate, instrument, timepoint, processing date). Compare separation by technical vs biological factors. Strong batch effects show samples clustering by technical factors more than biological conditions.

#### Quantitative Metrics

| Metric                               | Description                                               | Interpretation                                                                                                                   |
| ------------------------------------ | --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Principal Component Regression (PCR) | Weighted correlation of batch covariate with PCs          | 0-1, lower is better                                                                                                             |
| Average Silhouette Width             | Batch clustering in feature/PCA space                     | -1 to +1, closer to 0 indicates lower batch effects, closer to 1 for biological factors indicates stronger separation by biology |
| Technical Replicate Correlation      | Pairwise feature correlation of replicates across batches | -1 to +1, higher is better                                                                                                       |

**Decision**: Correct only if metrics indicate substantial batch effects (ca. >=5% cumulative explained variance by PCR)

### Apply Batch Correction

#### Prerequisites

- [ ] Data is log transformed and normalized
- [ ] Batch must not confounded with biological conditions of interest
- [ ] Missing values handled (impute or use NaN-tolerant batch correction method method)

#### Algorithms

| Algorithm        | Description                          | When to Use                                |
| ---------------- | ------------------------------------ | ------------------------------------------ |
| ComBat           | Empirical Bayes shift+scale model    | Default choice, handles small batches well |
| Median centering | Equalizes batch-wise feature medians | Simple cases, interpretable                |
| Harmony          | Iterative mixing in PCA space        | Large datasets, many batches               |

## 3. Validate Correction

Re-compute the same metrics used in assessment:

- PCR score should decrease
- Silhouette width should approach 0
- Technical replicate correlation should increase

If metrics do not improve, consider alternative algorithms or investigate confounding.
