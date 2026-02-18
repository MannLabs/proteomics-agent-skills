---
name: interpreting-biological-results
description: |
  Interpret biological results from omics analyses. Use when (1) performing overrepresentation analysis (ORA) on
  significant gene/protein lists, (2) running gene set enrichment analysis (GSEA) on ranked features, (3) querying STRING/UniProt
  for protein function, or (4) annotating clusters with pathway information.
---

## Overview

This skill applies when an upstream analysis (differential expression, clustering, PCA, etc.) has generated:

- A list of significant features (a "hit list") OR
- A ranked or scored list of all features (e.g., by log2 fold-change or test statistic) OR
- Clusters or modules of co-expressed/co-regulated features OR
- A specific gene or small set of features they want to understand functionally

Features might be protein names or gene names encoding the observed proteins.

The goal of this analysis is to leverage biological knowledge databases in connection with appropriate statistical methods to find higher-order associations between individual proteins.

## Context and Definitions

### Method Selection

Choose the appropriate methods based on which information is available

| Input | Example | Recommended primary methods |

| A Set of Features | e.g., a list of significantly differentially expressed proteins below an FDR cutoff | Overrepresentation Analysis, STRING DB search |
| A ranked list of features | e.g. genes ranked by their logfoldchanges or t-statistic | Gene Set Enrichment Analysis, GSVAMultiple |
| gene lists | e.g., differentially expressed proteins per cluster or cohort | Overrepresentation Analysis per cluster |
| Single gene of interest | e.g. a protein that is unknown to the analyst | UniProt, STRING (single query), literature search on PubMed |

### Handling Protein Groups

Proteomics search engines often report protein groups (e.g., `P12345;Q67890`) when peptides map to multiple proteins. Before enrichment analysis:

| Strategy | When to use | Implementation |

| **Take first** | Default for most analyses; assumes first entry is the most confident identification | Split on `;` and keep first ID |
| **Explode** | When it is important to identify all putatively involved gene sets with high sensitivity | Duplicate row for each protein in group |
| **Drop ambiguous** | When high confidence is critical | Remove rows with multiple proteins |

Use "take first" as the default strategy unless the experimental context requires otherwise.

### Available biological knowledge databases

| Database | Type | Comprehensiveness | Annotation Quality | Utility |

| Gene Ontology Biological Processes (GOBP) | Collection of gene sets and gene annotations | 5/5 | 4/5 | Use as default choice for overrepresentation analysis and gene set enrichment analysis |
| Reactome | Collection of highly curated gene sets and gene annotations | 3/5 | 5/5 | Use as default choice for overrepresentation analysis and gene set enrichment analysis |
| Kyoto Encyclopedia of Genes and Genomes (KEGG) database | Collection of biological pathways (human centric) and gene annotations | 3/5 | 5/5 | Use for human data if signaling pathways are of highest interest |
| MSigDB | Meta-resource that collects many geneset resources (e.g. gene ontology, KEGG, etc.) | 5/5 | 3/5 |
| STRING database | Collection of proteins and their interactors | 4/5 | 4/5 | Use when you want to identify interactors of a protein |
| UniPort | Annotated collection of encoded proteins across many organisms | 5/5 | 3/5 | Use when you want to get information of one protein, e.g. subcellular location, disease association, or function |
| Human Protein Atlas | 4/5 | 3/5 | Use when you want to learn about the expression of proteins in specific cell types |
| Open Targets | Annotated collection of genes, proteins, and their drug interactors | 3/5 | 5/5 | Use when you want to learn about implications of proteins in human disease and drug interactions |

**Caveats** Databases are incomplete and are missing functional links, limiting recall. Functional associations between proteins as described by the databases are highly context-dependent (e.g. dependent on cell type) and might be incorrect for the concrete biological setting. ALWAYS scrutinize the obtained results.

### Identifier Mapping

Most knowledge databases (GO, Reactome, KEGG, MSigDB) use **gene symbols** (e.g., `TP53`, `EGFR`), while proteomics data typically contains **UniProt accessions** (e.g., `P04637`, `P00533`).

**Resolution strategy:**

1. Check if the dataset already contains a `gene_name` or `gene_symbol` column and use it directly
2. If only UniProt IDs are available, map them using the UniProt ID mapping service: https://www.uniprot.org/id-mapping. See [./references/uniprot-id-mapping.md](./references/uniprot-id-mapping.md)

**Common pitfalls:**

- Unmapped IDs reduce statistical power — report the mapping rate (aim for >90%)
- Deprecated UniProt IDs may fail to map — check for secondary accessions
- One UniProt ID can map to multiple gene symbols (isoforms) — take the primary gene name

### Analysis tools

The `decoupler` Python package provides methods to run overrepresentation analysis and `gene set enrichment analysis`. For a reference on its usage, see `./references/decoupler.py`

## Workflow

### Checklist

Copy this checklist and track progress:

```
Analysis step progress:
- [ ] Step 1: Identify relevant methods
- [ ] Step 2: Identify relevant databases
- [ ] Step 3: If necessary: Map uniprot IDs to gene names
- [ ] Step 4: Perform analysis
- [ ] Step 5: Evaluate results

If additional methods/databases are desired: Go back to step 1
```

### Detailed Instructions

#### Step 1: Identify relevant methods

Select the appropriate analysis tool based on the available type of data (see [Method Selection](#method-selection)).

#### Step 2: Identify relevant databases

Prioritize which knowledge database should be used based the main goal of the analysis.

For example: If the main goal of the analysis is to find potential drugs for repurposing, focus on identifying individual, highly dysregulated genes and their drug interactors and annotate them with open targets.

#### Step 3: Map uniprot IDs to gene names

Ensure that the original differential expression result table does not contain gene names. Use the uniprot-id-mapping reference to map uniprot identifer to gene names via the Uniprot API.

#### Step 4: Perform analysis

Perform the analysis with the appropriate method and database. Use appropriate software packages, as described in the [Analysis Tools](#analysis-tools) section.

**Make sure** that the false discovery rate is correctly controlled with Benjamini-Hochberg correction.

Store the results as a long table with the following columns:

- Condition (string): Name of condition
- Geneset (string): Name of gene set.
- Score (float): Indicates directionality (up/down) of the regulation.
- False discovery rate (float): Benjamini-Hochberg corrected p-values.

#### Step 5: Evaluate analysis

Validate enrichment results before reporting:

1. **Check biological plausibility**: Verify that enriched terms relate to the experimental perturbation (e.g., "apoptosis" terms after drug treatment known to induce cell death)
2. **Cross-validate across databases**: Run ORA/GSEA on at least two independent databases (e.g., GO and Reactome). Robust signals appear in both.
3. **Inspect leading-edge genes**: For GSEA, examine the genes driving enrichment. Confirm they show the expected direction of regulation.
4. **Evaluate gene set size**: Distrust very small sets (n<5, noisy) or very large sets (n>500, non-specific).
5. **Verify key members**: Look up 2–3 top-contributing proteins in UniProt/in the PubMed literature. Confirm their known functions align with the enriched term.

**If no significant enrichment is found:**

- Confirm identifier mapping succeeded (>90% mapped)
- For ORA: verify the background set matches all measured proteins (not the whole genome)
- Consider relaxing the significance threshold for the input gene list
- A genuine negative result is also informative — report it as such
