---
name: planning-proteomics-analysis
description: |
  Plans end-to-end proteomics analysis workflows including data reading, QC, preprocessing, statistical analysis, and biological interpretation. Use at the start or during analysis to orchestrate progress.
---

# Planning Proteomics Data Analysis

This skill provides a high-level framework for **planning** proteomics data analysis in six sequential phases. This skill links to specialized skills for each phase.

## Analysis Workflow

### Phase 1: Reading proteomics data
Read proteomics search engine outputs (e.g., PSM and protein tables).
* **Skill:** `reading-proteomics-data`

### Phase 2: Controlling data quality
Check technical quality (e.g., feature counts, missing values) and filter out decoys, outlier samples, or low-evidence features.
* **Skill:** `performing-proteomics-quality-control`

### Phase 3: Preprocessing data
Apply data transformations including normalization, imputation, or batch correction.
* **Skills:** `normalizing-proteomics-data`, `imputing-proteomics-data`, `correcting-proteomics-batch-effects`

### Phase 4: Performing statistical analysis
Identify significant changes between conditions using differential expression analysis to obtain p-values, fold-changes, volcano plots, and more.
* **Skill:** `performing-statistical-analysis`

### Phase 5: Interpreting biological results
Translate regulated protein lists into biological pathways (e.g., Over-Representation Analysis (ORA), Gene Set Enrichment Analysis (GSEA)).
* **Skill:** `interpreting-biological-results`

### Phase 6: Formulating biological findings
Synthesize results into findings to understand potential connections between proteins and their roles in biological mechanisms or disease genesis.
* **Skill:** `formulating-biological-findings`

## Instructions

1. **Determine Phase:** Identify where the user stands in their analysis: at the beginning or in the middle.
2. **Strategy Selection:** Think through the analysis **workflow** and use the **knowledge** in the specific skill for each phase.
3. **Plan:** Plan each step considering the study context and provided source data. Outline the strategy and parameters you would use for each step. Define success criteria for each phase, which Claude must verify before moving the next phase.
4. **Verify:** Ensure the plan includes the following sequence. Copy the following checklist into the chat to track analysis progress:

```markdown
Analysis Progress:
- [ ] Phase 1: Reading proteomics data
- [ ] Phase 2: Controlling data quality
- [ ] Phase 3: Preprocessing data: Normalizing, Imputing, Batch effects
- [ ] Phase 4: Performing statistical analysis
- [ ] Phase 5: Interpreting biological results
- [ ] Phase 6: Formulating biological findings

```

## Example Analysis Plan
1. Reading proteomics data: Loading pg_matrix.tsv.
- Success Criteria: Engine is identified as DIA-NN; columns are mapped to standardized names; decoys are removed; the resulting matrix has samples as rows and numeric intensities as values.
2. Controlling data quality: Filter out proteins with fewer than 2 peptides. Compute per-sample total intensity and feature count; flag and remove MAD-based outliers (N = 3) on both metrics. Then apply a 70% feature completeness threshold (bulk tissue default).
- Success Criteria: All features with less than 70% completeness are removed
3. Preprocessing data:
- Normalization: Log2-transform intensities (treating zeros as NA). Assess normalization need by computing correlation between PC1 and log10(total intensity per sample). If correlation is strong, apply median centering.
Evaluate with Pooled Median Absolute Deviation (PMAD)
- Imputation: Plot feature intensity versus missingness rate to classify the dominant pattern (MNAR vs. MCAR). If MNAR-dominant, apply MinProb imputation; if MCAR-dominant, apply BPCA. Validate by comparing intensity distributions
before and after imputation and checking PCA stability via Procrustes analysis.
- Success Criteria: PMAD decreases & PC–intensity correlation should drop after normalization; imputed value distribution does not create artificial modes; PCA structure remains stable after imputation; no NaN values remain.
4. Performing statistical analysis: Define WT as control and KO as treatment.
Apply a two-sided t-test (moderated via limma's eBayes since fewer than 5 replicates per group) to compute log2 fold-changes and raw p-values. Apply Benjamini-Hochberg correction to obtain q-values. Mark proteins as significant at q-value < 0.05 and |log2 fold-change| > 1. Generate a volcano plot and a z-score-normalized heatmap of significant proteins with hierarchical clustering.
- Success Criteria: A results table (results_statistical_analysis.tsv) exists with columns protein_group, gene_name, log2_fold_change, p_value, -log10_pvalue, q_value, is_significant. Heatmap shows replicates clustering by condition.
