---
name: proteomics-io
description: Read and filter proteomics search engine outputs (PSM tables and protein group matrices) from DIA-NN, MaxQuant, Spectronaut, AlphaDIA, MSFragger, Sage, and other engines. Use when (1) ingesting proteomics data files (.tsv, .txt, .csv) for analysis, (2) mapping search engine column names to standardized fields, (3) identifying which search engine produced a file, (4) extracting protein/peptide intensities, or (5) preparing proteomics data for downstream statistical analysis.
---

# Proteomics Search Engine Output Reader

Read and interpret proteomics search engine outputs for downstream analysis.

## Quick Reference

**Two main output types:**

1. **PSM tables** (long format) - One row per peptide-spectrum match, contains all feature levels
2. **Protein group tables** (wide format) - Features x samples matrix, protein-level only

**Prefer protein group tables** when available and protein-level analysis is requested.

## Reading PSM Tables

PSM tables map peptide sequences to observed spectra with intensities.

**Key fields to extract:**

- `sequence` / `modified_sequence` - Peptide identification
- `proteins` / `uniprot_ids` / `genes` - Protein mapping
- `intensity` / `precursor_intensity` - Quantification
- `fdr` / `score` - Quality metrics
- `raw_name` - Sample identifier
- `charge`, `rt`, `mobility` - Physical properties

**Column mapping:** See [references/psm-columns.md](references/psm-columns.md)

## Reading Protein Group Tables

Protein group tables are wide-format matrices: features (rows) x samples (columns).

### Engine-Specific File Formats

| Engine      | Format | File type           | Protein ID Column                                               | Intensity Column Pattern                       |
| ----------- | ------ | ------------------- | --------------------------------------------------------------- | ---------------------------------------------- |
| AlphaPept   | HDF5   | `.csv`, `.hdf`      | Unnamed index at position 0, `pandas` parses it to `Unnamed: 0` | `{sample}_LFQ`, `{sample}_MS1`, `{sample}_MS2` |
| DIA-NN      | TSV    | `.tsv`, `.parquet`  | `Protein.Group`                                                 | Sample names                                   |
| MaxQuant    | TXT    | `proteinGroups.txt` | `Protein IDs`                                                   | `LFQ intensity {sample}`, `Intensity {sample}` |
| Spectronaut | TSV    | `.tsv`              | `PG.ProteinGroups`                                              | `[{index}] {sample}.PG.Quantity`               |
| AlphaDIA    | TSV    | `.tsv`              | `pg`                                                            | Sample names                                   |

**Column mapping:** See [references/pg-columns.md](references/pg-columns.md) for a detailed description of the columns

### Intensity Types

Search engines may report multiple intensity types:

| Type       | Description                 | Use Case                     |
| ---------- | --------------------------- | ---------------------------- |
| MS1        | Direct peak area            | DDA, Low-input proteomics    |
| MS2        | Sum of fragment intensities | Standard DIA                 |
| LFQ/MaxLFQ | Label-free normalized       | Cross-run comparison         |
| iBAQ       | Intensity-based absolute    | Protein abundance estimation |

**For comparing across samples, prefer LFQ-normalized intensities.**

## Quality Filtering

Apply standard filters before analysis:

### FDR filtering 
In PSM tables, filter for features with a high FDR: typically 1% 

### Decoy removal 
Remove decoys which are either indicated by a specific column (See columns that map to the standardized column `is_decoy`) or which are contain a specific indicator in the `sequence` column: e.g. a `REV_` prefix

### Contaminant removal
If applicable (e.g. for MaxQuant): Remove contaminants which are indicated by a designated column.
