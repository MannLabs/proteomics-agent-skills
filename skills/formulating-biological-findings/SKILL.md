---
name: formulating-biological-findings
description: | 
    Drawing findings from proteomics analysis. Use when computational data analysis is complete. Use for investigating biological or clinical impact.

---

# Overview

This skill guides the transition from the analysis of differentially expressed proteins (upregulated and downregulated) to the synthesis of findings that highlight protein interactions, pathway cross-talk, or potential roles in disease.

## Use When

Use when all preprocessing steps (including data reading, QC, preprocessing, statistical testing) are complete, and only the biological/clinical interpretation remains.

# Workflow

## Checklist

Copy this checklist to track your progress:

```
finding Generation Progress:
- [ ] Step 1: Functional Analysis
- [ ] Step 2: Ontology Analysis
- [ ] Step 3: Critical Review
- [ ] Step 4: Biological Context
- [ ] Step 5: Focused Analysis & Prioritization
- [ ] Step 6: Deeper Analysis
- [ ] Step 7: Documentation

```

## Detailed Instructions

### Step 1: Functional Analysis

- Identify relationships between differentially expressed proteins (DEPs) using your broad biological knowledge and functional protein annotations (e.g. in uniprot)
- Investigate whether differentially expressed proteins are functionally related, e.g. via biochemical pathways or gene sets, or protein complexes.

### Step 2: Ontology Analysis

- Interpret enrichment results (e.g., GSEA/ORA) if available
- Examine which cellular processes are most affected based on protein changes
- Identify shared proteins between pathways (leading edges) as potential regulatory hubs

### Step 3: Critical Review

- Review all collected information
- Flag contradictions between different analyses
- Identify repeating patterns across analyses

### Step 4: Biological Context

- Consider the experimental design and research question for interpretation
- Explain implications of changes across biological scales (organelles, cells, organs, organism)
- Assess how these changes might affect overall biological function

### Step 5: Focused Analysis & Prioritization

- Recommend **3–5 key aspects** for further investigation (e.g., specific ontology terms, mechanisms, or homeostatic pathways)
- Explain the scientific rationale for each recommendation
- Select the most promising aspect for deep-dive analysis in the next step

### Step 6: Deeper Analysis (Post-Selection)

- List all proteins involved in the selected aspect
- Gather all available information on key proteins with the tool UniProt
- Retrieve quantitative information for all proteins within the identified pathways or functional units
- Place findings within the context of the current understanding in the field
- Identify results that either confirm or challenge existing biological models
- **Develop formal findings** explaining the observed patterns
- Suggest follow-up experiments to validate these findings

### Step 7: Documentation

- Compile findings into an `observed_findings.md` file.
- Each formal finding from step 6 should be summarized in a table with the following content:

| Finding | Related proteins | Evidence from Database search + Literature | (If applicable): Contradicting evidence | Suggested follow up experiment

# Success Criteria

- findings must be supported by both the protein change and the known biological function
- An `observed_findings.md` file must be generated
