---
name: reading-proteomics-data
description: Read proteomics search engine outputs (PSM tables, protein matrices) from search engines like DIA-NN, MaxQuant, Spectronaut, AlphaDIA, MSFragger, Sage. Use for ingesting data, mapping columns to standard names, and initial filtering.
---

# Proteomics Search Engine Output Reader

## 1. Context

### Table Types

| Type                                   | Format                    | Feature level                                              | Use When                                         |
| -------------------------------------- | ------------------------- | ---------------------------------------------------------- | ------------------------------------------------ |
| **Peptide spectrum match (PSM) table** | Long (one row per match)  | Precursor, Peptides (optional), Proteins, Genes (optional) | Peptide-level analysis, PTMs, custom aggregation |
| **Protein Group (PG) matrix**          | Wide (proteins × samples) | Proteins, Genes (Optional)                                 | Protein-level analysis                           |

**Use PG matrices** for protein- and gene-level analyses, if available

### Engine Detection Signatures

**Column Mapping References** contain the mapping of search-engine columns to standardized columns. Multiple column names might map to the same standardized column name, depending on the search engine version

- [references/psm-columns.md](references/psm-columns.md) — PSM table mappings
- [references/pg-columns.md](references/pg-columns.md) — PG matrix mappings

| Engine      | Key Columns                                               | Typical Files                       |
| ----------- | --------------------------------------------------------- | ----------------------------------- |
| DIA-NN      | `Precursor.Id`, `Protein.Group`, `Run`                    | `pg_matrix.tsv`, `report.tsv`       |
| MaxQuant    | `Raw file`, `Protein IDs`                                 | `proteinGroups.txt`, `evidence.txt` |
| Spectronaut | `PG.ProteinGroups`, `R.FileName`                          | `*_Report.tsv`                      |
| AlphaDIA    | `pg`, `precursor.idx`, `run`                              | `pg_matrix.tsv`                     |
| Sage        | `filename`, `stripped_peptide`, `sage_discriminant_score` | `results.sage.tsv`                  |
| MSFragger   | `Protein ID`, `Spectral Count`                            | `combined_protein.tsv`, `psm.tsv`   |
| AlphaPept   | `Unnamed: 0`, `_LFQ` suffix                               | `results.hdf`                       |

### Intensity Types

| Type           | Use Case                            |
| -------------- | ----------------------------------- |
| **LFQ/MaxLFQ** | Cross-sample comparison (preferred) |
| **MS1**        | Precursor area (DIA/DDA)            |
| **MS2**        | Fragment-based quantification (DIA) |

**Use LFQ-normalized intensities** for inter-sample comparisons, if available.

## 2. Workflow Checklist

- [ ] **Identify format:** `.tsv`/`.csv`/`.parquet`/`.hdf`, search engine (based on signature columns), and table type (PSM table vs. PG table, based on long vs. wide format)
- [ ] **Read file:** Use appropriate table parsing engine
- [ ] **Map columns:** See [references/psm-columns.md](references/psm-columns.md) or [references/pg-columns.md](references/pg-columns.md)
- [ ] **Filter (PSM only):** Remove rows where `fdr > 0.01`
- [ ] **Remove decoys:** Filter by decoy indicator column OR protein ID prefix (`REV_`, `DECOY_`)
- [ ] **Remove contaminants:** Filter protein ID prefix (`CON_`, `contaminant_`)
- [ ] **Pivot and transpose** Validate that table is in wide format with samples as rows and features as columns (scikit-learn convention)
- [ ] **Validate:** Confirm that table contains numeric intensities

## 3. Troubleshooting

| Issue                                                   | Solution                              |
| ------------------------------------------------------- | ------------------------------------- |
| MaxQuant decoy indicator `Reverse` uses `+` not boolean | Filter: `df['Reverse'] != '+'`        |
| Spectronaut column names vary by export schema          | Inspect actual columns                |
| AlphaPept HDF5 requires key                             | Use `key='protein_table'`             |
| Zero values may mean "not detected"                     | Treat 0 as NA before statistics       |
| Decoy prefix is on protein ID, not sequence             | Check `proteins`/`uniprot_ids` column |
