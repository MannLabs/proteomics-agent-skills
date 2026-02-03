---
name: imputing-proteomics-data
description: Impute missing values in protein-level proteomics data matrices. Use when (1) preparing proteomics data for downstream analyses requiring complete matrices (PCA, batch correction), (2) evaluating whether imputation is needed, (3) selecting appropriate imputation methods, or (4) assessing imputation quality. Does NOT cover normalization or batch correction.
---

# Imputing Proteomics Data

Impute missing values in protein intensity matrices for downstream analysis requiring complete data.

## When to Impute

**Impute when:**

- Downstream analysis requires complete data (e.g. PCA, COMBAT batch correction)
- Missingness rate is moderate (<30-50% per feature)

**Do NOT impute when:**

- Downstream methods handle missing data natively (mixed-effects models, limma)
- Feature missingness is high - instead remove highly missing features with low data support.

## Key missingness patterns

- **MNAR (Missing Not At Random)**: Low-abundance proteins below detection limit. Shows correlation between intensity and missingness.
- **MAR (Missing At Random)**: Ion suppression, peptide competition. More common in DDA.
- **MCAR (Missing Completely At Random)**: Stochastic dropouts. Random pattern.

## Imputation Workflow

Copy this checklist and track progress:

```
Analysis step progress:
- [ ] **Prepare data**
- [ ] **Assess** missingness patterns
- [ ] **Select** method
- [ ] **Apply imputation**
- [ ] **Validate** imputation quality
      If quality criteria not met: Restart at step `assess`
```

## Workflow

### Prepare data

**Remove** features with high missingness before imputation due to little data support. For typical datasets, features with <50% completeness should be removed. **Assess missingness within biological groups**, not globally. For example, a cell-type marker "missing" in 90% of cells but present in 100% of that cell type is informative

**Ensure that data is log-transformed** data if not already as most methods assume log-scale.

### Assess missingness patterns

Before imputation, assess missingness patterns.

**Calculate missingness rates** per feature.

**Visualize intensity vs missingness**. If missingness is higher for features with low intensity, this corresponds to a MNAR patterns. If the missingness is roughly independent, this corresponds to an MCAR or MAR pattern.

### Method Selection

Rational: Prefer methods that consider the global data structure over methods that only consider local
structure over methods that provide a single point estimate for all samples.

| Missingness Pattern                   | Recommended Methods (in recommended priority)                |
| ------------------------------------- | ------------------------------------------------------------ |
| MCAR dominant (high completeness)     | BPCA > Random Forest > KNN > median imputation               |
| MAR                                   | BPCA > > Random Forest > KNN > median imputation             |
| MNAR dominant (many low-abundance)    | Density Probabilty Estimation (DPC/LIMPA) > MinProb > MinDet |
| Datasets with many (ca. >500) samples | PIMMS (autoencoder)                                          |

### Quality Assessment

Evaluate imputation success:

1. **Distribution comparison**: Imputed values should match the overall intensity distribution (not create artificial modes)
2. **PCA stability**: Compare PCA before/after imputation using Procrustes analysis
3. **Covariance preservation**: Frobenius norm between original and imputed covariance matrices

Red flags:

- Imputed values clustered at single point (MinDet/MinProb artifacts)
- Sample clustering changes dramatically after imputation
- Variance inflation in highly missing features
