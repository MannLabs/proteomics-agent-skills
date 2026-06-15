---
name: using-proteomics-skills
description: |
  Meta-skill for discovering, orchestrating, and sequencing the proteomics analysis skills. Use at the start of any proteomics task to identify which phase applies and which specialized skill to invoke, and to plan and track an end-to-end workflow (reading, QC, preprocessing, statistics, interpretation, findings).
---

# Using Proteomics Skills

This is the **meta-skill** that governs how all other proteomics skills are discovered, sequenced, and applied. Each specialized skill encodes the process an expert proteomics analyst would follow for one phase of work. This skill maps an incoming task to the right phase, routes to the corresponding skill, and keeps the overall analysis on track.

When a proteomics task arrives, identify the current **phase** and apply the corresponding skill. Do not improvise a phase that a dedicated skill already covers.

## Skill Discovery

Route the task using the decision logic below:

- **Don't know what the data is or which engine produced it yet?** → `reading-proteomics-data`
- **Have a matrix but unsure if samples/features are trustworthy?** → `performing-proteomics-quality-control`
- **Need to make samples comparable / stabilize variance?** → `normalizing-proteomics-data`
- **Missing values blocking a method that needs a complete matrix?** → `imputing-proteomics-data`
- **Suspect plate, instrument, or time-point bias?** → `correcting-proteomics-batch-effects`
- **Ready to test which proteins change between conditions?** → `performing-statistical-analysis`
- **Have a list of regulated proteins and need biological meaning?** → `interpreting-biological-results`
- **Need to synthesize results into a narrative or hypothesis?** → `formulating-biological-findings`
- **Writing or finalizing any analysis code?** → `applying-code-standards` (cross-cutting)
- **Implementing any of the above in Python?** → `analyzing-proteomics-data` (use `alphapepttools`, cross-cutting)

If the request spans multiple phases (e.g. "run the full analysis"), follow the **Lifecycle Sequence** below in order.

## Core Operating Behaviors

These apply across every phase and are non-negotiable:

1. **Surface assumptions about experimental design first.** Confirm groups, replicates, controls vs. treatments, and feature level before acting. Ask when unclear; do not guess.
2. **Run diagnostics before transforms.** Assess *whether* normalization, imputation, or batch correction is needed before applying any. Do not transform data by default.
3. **Verify each phase before advancing.** Define and check explicit success criteria for a phase before moving to the next.
4. **Use `alphapepttools`, not custom implementations,** for proteomics workflows. Prefer the established tooling over hand-rolled code.
5. **Keep scope disciplined.** Touch only the phase the task requires; do not silently re-run or alter upstream steps.
6. **Apply code standards to every output.** Invoke `applying-code-standards` when writing or finalizing analysis code.

## Failure Modes to Avoid

- Imputing before assessing the missingness pattern (MNAR vs. MCAR).
- Normalizing or batch-correcting without diagnostics indicating they are needed.
- Treating zeros as real intensities instead of missing values (NA).
- Skipping QC and carrying decoys, outlier samples, or low-evidence features into statistics.
- Applying batch correction on anything other than the protein level, or without a diagnostic justification.
- Running statistics on an incomplete or unnormalized matrix when the method assumes otherwise.
- Advancing to the next phase without checking the current phase's success criteria.
- Re-implementing functionality that `alphapepttools` already provides.
- Drawing biological conclusions before the computational analysis is complete and verified.

## Lifecycle Sequence

The typical end-to-end workflow runs in six sequential phases:

### Phase 1: Reading proteomics data
Read search engine outputs (e.g., PSM and protein tables).
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

## Quick Reference

| Phase | Skill | What it does |
| --- | --- | --- |
| 1. Read | `reading-proteomics-data` | Ingest engine outputs, map columns, initial filtering |
| 2. QC | `performing-proteomics-quality-control` | Remove decoys, outlier samples, low-evidence features |
| 3. Preprocess | `normalizing-proteomics-data` | Make samples comparable, stabilize variance |
| 3. Preprocess | `imputing-proteomics-data` | Fill missing values for methods needing complete matrices |
| 3. Preprocess | `correcting-proteomics-batch-effects` | Diagnose and correct technical batch bias |
| 4. Statistics | `performing-statistical-analysis` | Differential expression, FDR, volcano plots |
| 5. Interpret | `interpreting-biological-results` | ORA/GSEA, pathway and function annotation |
| 6. Findings | `formulating-biological-findings` | Synthesize biological narrative and hypotheses |
| Cross-cutting | `analyzing-proteomics-data` | Implement workflows with `alphapepttools` (AnnData) |
| Cross-cutting | `applying-code-standards` | Code quality for reproducible analysis |

## Instructions

1. **Determine Phase:** Identify where the user stands in their analysis: at the beginning or in the middle. Use **Skill Discovery** to route to the right skill.
2. **Strategy Selection:** Think through the analysis **workflow** and use the **knowledge** in the specific skill for each phase.
3. **Plan:** Plan each step considering the study context and provided source data. Outline the strategy and parameters you would use for each step. Define success criteria for each phase, which Claude must verify before moving to the next phase.
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