---
name: imputing-proteomics-data
description: Impute missing values in protein-level proteomics data matrices. Use when (1) preparing proteomics data for downstream analyses requiring complete matrices (PCA, batch correction), (2) evaluating whether imputation is needed, (3) selecting appropriate imputation methods based on missingness patterns, or (4) assessing imputation quality. Does NOT cover normalization or batch correction.
---

# Imputing Proteomics Data

Impute missing values in protein intensity matrices for downstream analysis requiring complete data.

## When to Impute

**Impute when:**

- Downstream analysis requires complete data (PCA, COMBAT batch correction)
- Missingness rate is moderate (<30-50% per feature)

**Do NOT impute when:**

- Downstream methods handle missing data natively (mixed-effects models, limma)
- Feature missingness is high.

## Key missingness patterns

- **MNAR (Missing Not At Random)**: Low-abundance proteins below detection limit. Shows correlation between intensity and missingness.
- **MAR (Missing At Random)**: Ion suppression, peptide competition. More common in DDA.
- **MCAR (Missing Completely At Random)**: Stochastic dropouts. Random pattern.

## Imputation Workflow

- [ ] **Filter** features with high missingness before imputation
- [ ] **Ensure that data is log-transformed** data if not already (most methods assume log-scale)
- [ ] **Assess** missingness patterns and select method
- [ ] **Impute** the data
- [ ] **Validate** imputation quality

## Details

### Filtering Before Imputation

Key question: Is missingness technical (below detection limit → impute) or biological (not expressed → do NOT impute)?

**Homogeneous samples** (replicates of same condition/tissue):

- Filter features with ca. >50% global missingness

**Heterogeneous samples** (mixed cell types, multi-tissue):

- Assess missingness within biological groups, not globally
- A cell-type marker "missing" in 90% of cells but present in 100% of that cell type is informative

### Missingness Assessment

Before imputation, assess missingness patterns:

1. **Calculate missingness rates** per feature and per sample
2. **Visualize intensity vs missingness** to identify MNAR patterns (low-intensity features have higher missingness)
3. **Check for sample outliers** with abnormally high missingness

### Method Selection

| Missingness Pattern                | Recommended Methods                        |
| ---------------------------------- | ------------------------------------------ |
| MCAR dominant (high completeness)  | KNN, median, Gaussian, BPCA, Random Forest |
| MNAR dominant (many low-abundance) | MinProb, MinDet, LIMPA                     |
| Large datasets                     | PIMMS (deep learning)                      |

### Quality Assessment

Evaluate imputation success:

1. **Distribution comparison**: Imputed values should match the overall intensity distribution (not create artificial modes)
2. **PCA stability**: Compare PCA before/after imputation using Procrustes analysis
3. **Covariance preservation**: Frobenius norm between original and imputed covariance matrices

Red flags:

- Imputed values clustered at single point (MinDet/MinProb artifacts)
- Sample clustering changes dramatically after imputation
- Variance inflation in highly missing features
