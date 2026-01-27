---
name: proteomics-qc-skill
description: Quality control for protein-level proteomics data. Use when filtering outlier samples or low-evidence features before downstream analysis. Covers PSM artifact removal, sample QC, feature-level filtering. Does NOT cover normalization, batch correction, or imputation.
---

# Proteomics Quality Control

Filter outlier samples and unsupported features from protein-level quantification matrices.

## Context and Definitions

### Study Types

| Type         | Min features/sample | Completeness | Intensity metric |
| ------------ | ------------------- | ------------ | ---------------- |
| Single-cell  | >500-600 proteins   | 10-15%       | Total intensity  |
| Bulk tissue  | No strict minimum   | 50-70%       | Total intensity  |
| Plasma/serum | No strict minimum   | 20-50%       | Median intensity |

### Key Metrics

- **Completeness**: Fraction of samples with non-missing values per feature
- **MAD outlier**: Sample flagged when `|value - median| > N × MAD`
- **Technical replicate QC**: CV < 20-30%, Pearson R > 0.9 expected
- **Decoy prefixes**: `REV_`, `DECOY_`, or decoy indicator column

### Defaults

| Parameter           | Default         | Adjust when                                    |
| ------------------- | --------------- | ---------------------------------------------- |
| FDR threshold       | 0.01            | Standard value                                 |
| MAD multiplier (N)  | 3               | Lower (e.g. ~2) strict, higher (~5) permissive |
| Min unique peptides | 2               | 2+ for validation studies                      |
| Completeness        | Study-dependent | Must match expected biological prevalence      |

## Checklist

1. [ ] Clarify study type, completeness threshold, outlier handling
2. [ ] Remove decoys and apply FDR filter
3. [ ] Optional: Remove proteins with few unique supporting peptides
4. [ ] If technical replicates exist: Assess technical replicate reproducibility
5. [ ] Flag/remove sample outliers by intensity
6. [ ] Flag/remove sample outliers by feature count
7. [ ] Filter features below completeness threshold
8. [ ] Generate QC report

**Order matters**: Steps 2-3 (PSM) → Steps 4-6 (Sample QC) → Step 7 (Feature QC). Sample QC must precede feature QC because outlier samples inflate apparent missingness.

## Instructions

### Step 1: Clarify Parameters

Before starting, be aware of:

- **Study type**: Single-cell, bulk, or plasma?
- **Completeness threshold**: What minimum biological prevalence is expected? (e.g., 10% cell population → expect 90% missingness for its markers)
- **Outlier handling**: Remove samples or flag only?
- **Technical replicates**: Present in dataset?

### Steps 2-3: PSM Artifact Removal

- Remove rows with decoy prefixes or decoy indicator = True
- Remove rows with FDR >= threshold (default 0.01)
- Optional: Remove proteins with < 2 unique peptides

### Step 4: Technical Replicate QC (if applicable)

- Compute CV and Pearson R between replicates
- Flag replicate sets with CV > 30% or R < 0.9

### Steps 5-6: Sample QC

Compute per-sample:

- **Intensity**: Total (or median for plasma/serum)
- **Feature count**: Number of non-missing proteins

For each metric, flag samples where `|value - median| > N × MAD`.

### Step 7: Feature QC

- Compute completeness per feature (after removing flagged samples)
- Remove features below threshold

Example: 70% completeness filter removes proteins present in <70% of samples—inappropriate if studying a 10% subpopulation.

### Step 8: Generate QC Report

Output:

1. **Summary table**: Sample ID, intensity, feature count, flag status, flag reason
2. **Histogram of each metric**: Before/after feature filtering
3. **Filtered matrix**: Ready for downstream processing
