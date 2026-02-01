---
name: performing-proteomics-quality-control
description: Perform quality control for protein-level proteomics data. Use when filtering outlier samples or low-evidence features before downstream analysis. Covers PSM artifact removal, sample QC, feature-level filtering. Does NOT cover normalization, batch correction, or imputation.
---

# Proteomics Quality Control

Goal: Remove outlier samples and unsupported features from protein-level quantification matrices.

## Context and Definitions

### Key Metrics

- **Decoy prefixes**: `REV_`, `DECOY_`, or boolean decoy indicator column
- **Quality Control (QC) for technical replicates**: Remove technical replicates when coefficient of Variation (CV) > 20-30% or Pearson Correlation R < 0.9
- **Median absolute deviation (MAD)-based outlier detection**: An observation _x_ from a set of observations _X_ is flagged as an outlier when `|x - median(X)| > N × MAD(X)`, where _N_ is a user-defined factor. MAD is calculated as the median of absolute deviations from the median: `MAD(X) = median(|X - median(X)|)`.
- **Feature Completeness**: Feature-wise fraction of samples with non-missing values.

### Study Types

| Type         | Min features/sample | Completeness | Intensity metric |
| ------------ | ------------------- | ------------ | ---------------- |
| Single-cell  | >500-600 proteins   | 10-15%       | Total intensity  |
| Bulk tissue  | No strict minimum   | 50-70%       | Total intensity  |
| Plasma/serum | No strict minimum   | 20-50%       | Median intensity |

### Defaults

| Parameter                 | Default                                      | Adjust when                                                                                                                                                                                                          |
| ------------------------- | -------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| False discovery threshold | 0.01                                         | Standard value                                                                                                                                                                                                       |
| MAD multiplier (N)        | 3                                            | Lower (<3) strict, higher (>3) more permissive                                                                                                                                                                       |
| Min unique peptides       | 2                                            | >2 for validation studies                                                                                                                                                                                            |
| Feature Completeness      | Study-dependent, typically between 10% - 70% | Must find a compromise between robustness and expected biological prevalence. If an effect is expected in a small subset of samples (e.g. a rare cell type) it should not be removed by feature completeness filters |

## Checklist

1. [ ] Clarify Parameters
2. [ ] Remove Low Confident Identifications
3. [ ] Remove Peptide Spectrum Match Artifacts
4. [ ] If technical replicates exist: Assess technical replicate reproducibility
5. [ ] Remove sample outliers
6. [ ] Filter features below completeness threshold
7. [ ] Generate QC report

**Order matters**: Steps 2-3 (PSM) → Steps 4-6 (Sample QC) → Step 7 (Feature QC). Sample QC must precede feature QC because outlier samples inflate apparent missingness.

## Instructions

### Step 1: Clarify Parameters

Before starting, check for:

- **Study type**: Single-cell, bulk, or plasma?
- **Completeness threshold**: What minimum biological prevalence is expected? (e.g., 10% cell population → expect 90% missingness for its markers)
- **Outlier handling**: Remove samples or flag only?
- **Technical replicates**: Present in dataset?

### Step 2: Remove Low Confident Identifications

- Remove rows with decoy prefixes or decoy indicator = True
- If applicable: Remove proteins with < 2 unique peptides

### Step 3: Remove Peptide Spectrum Match Artifacts

- Remove rows with false discovery rates >= false discovery threshold

### Step 4: Technical Replicate QC (if applicable)

- Compute CV and Pearson R between replicates
- Remove replicate sets with CV > 30% or Pearson R < 0.9

### Steps 5: Sample QC

Compute per-sample:

- **Intensity**: Total (or median for plasma/serum)
- **Feature count**: Number of non-missing proteins

For each metric, remove MAD outliers (see [Key Metrics](#key-metrics))

### Step 6: Feature QC

- Compute completeness per feature
- Remove features below completeness threshold

### Step 7: Generate QC Report

Output:

1. **Summary table**: That shows for each quality control step the number of samples and features before and after filtering. For an example see ./references/qc-summary.tsv
2. **Histogram of each metric**: Distribution of the respective metric before filtering on the left, and after filtering on the right.
3. **Filtered matrix**: With observations as rows and features as columns. Ready for downstream processing
