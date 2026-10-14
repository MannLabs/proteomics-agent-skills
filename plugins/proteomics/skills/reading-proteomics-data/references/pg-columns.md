# Protein Group Table Column Mapping

## Standardized Column Names

| Column                      | Description                                                        |
| --------------------------- | ------------------------------------------------------------------ |
| `uniprot_ids`               | UniProt IDs of proteins in the protein group (semicolon-separated) |
| `proteins`                  | UniProt names of proteins in the protein group                     |
| `genes`                     | Gene names (HGNC for humans, semicolon-separated)                  |
| `protein_candidates`        | UniProt IDs of all proteins matching before inference              |
| `description`               | Long text description of proteins                                  |
| `ensembl_ids`               | ENSEMBL IDs of proteins                                            |
| `source_db`                 | Source databases (UniProt, SwissProt, Ensembl)                     |
| `is_decoy`                  | Boolean decoy indicator                                            |
| `peptide_count`             | Number of peptides mapping to the protein group                    |
| `proteotypic_peptide_count` | Number of gene-specific peptides                                   |
| `precursor_id`              | Unique precursor identifier                                        |

## Search Engine Column Mapping

### AlphaDIA

| Standard    | AlphaDIA Column |
| ----------- | --------------- |
| uniprot_ids | `pg`            |

### DIA-NN

| Standard                  | DIA-NN Column               |
| ------------------------- | --------------------------- |
| uniprot_ids               | `Protein.Group`             |
| proteins                  | `Protein.Names`             |
| genes                     | `Genes`                     |
| protein_candidates        | `Protein.Ids`               |
| description               | `First.Protein.Description` |
| peptide_count             | `N.Sequences`               |
| proteotypic_peptide_count | `N.Proteotypic.Sequences`   |

### MaxQuant

| Standard    | MaxQuant Column |
| ----------- | --------------- |
| uniprot_ids | `Protein IDs`   |
| proteins    | `Protein names` |
| genes       | `Gene names`    |
| is_decoy    | `Reverse`       |

### Spectronaut

| Standard    | Spectronaut Column(s)                                     |
| ----------- | --------------------------------------------------------- |
| uniprot_ids | `PG.UniProtIds`                                           |
| proteins    | `PG.ProteinNames`, `PG_ProteinGroups`, `PG.ProteinGroups` |
| genes       | `PG.Genes`                                                |

### FragPipe

| Standard    | FragPipe Column |
| ----------- | --------------- |
| uniprot_ids | `Protein ID`    |
| proteins    | `Entry Name`    |
| genes       | `Gene Names`    |
| description | `Description`   |

### AlphaPept

AlphaPept has no standardized column names. The first column is **unnamed** and contains the raw FASTA header from the search database. Parse this header to extract protein metadata.

#### HDF structure

- **File format:** HDF5 (`.hdf`)
- **HDF5 keys:** `protein_table` (protein groups), `peptide_table` (peptides)
- **Protein ID column:** First column, unnamed (`Unnamed: 0` when read)
- **Protein ID format:** Raw FASTA header (e.g., `sp|P36578|RL4_HUMAN`)
- **Intensity suffixes:** `_LFQ` (normalized), `_MS1` (MS1 area), `_MS2` (MS2 sum)
- **For cross-sample comparison:** Use columns ending in `_LFQ`

#### FASTA header formats and parsing

| Format            | Example Header                 | Parsed Fields                                                |
| ----------------- | ------------------------------ | ------------------------------------------------------------ |
| UniProt           | `sp\|Q9NQT4\|EXOS5_HUMAN`      | source_db=`sp`, uniprot_ids=`Q9NQT4`, proteins=`EXOS5_HUMAN` |
| UniProt (minimal) | `Q0IIK2`                       | uniprot_ids=`Q0IIK2`                                         |
| ENSEMBL           | `ENSEMBL:ENSBTAP00000024146`   | source_db=`ENSEMBL`, ensembl_ids=`ENSBTAP00000024146`        |
| Decoy             | `REV__sp\|Q13085\|ACACA_HUMAN` | is_decoy=`True`, prefix `REV__` indicates decoy              |

**Protein groups:** Multiple proteins are comma-separated in the header. Parse each entry and join with semicolons.

Example: `sp|Q9H2K8|TAOK3_HUMAN,sp|Q7L7X3|TAOK1_HUMAN` yields:

- uniprot_ids: `Q9H2K8;Q7L7X3`
- proteins: `TAOK3_HUMAN;TAOK1_HUMAN`

### MZTab

| Standard    | MZTab Column  |
| ----------- | ------------- |
| uniprot_ids | `accession`   |
| description | `description` |
| source_db   | `database`    |

### directLFQ

| Standard     | directLFQ Column |
| ------------ | ---------------- |
| uniprot_ids  | `protein`        |
| precursor_id | `ion`            |
