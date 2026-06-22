# Proteomics Agent Skills

[Claude Code](https://claude.com/claude-code) Agent Skills for proteomics data
analysis, from the [Mann Labs](https://github.com/MannLabs).

These skills bring proteomics-specific expertise to quantitative mass-spectrometry
data. They handle the differences between search engines and guide a rigorous
analysis workflow built for protein-level data.

## Install (Claude Code)

```
/plugin marketplace add MannLabs/proteomics-agent-skills
/plugin install proteomics@proteomics-agent-skills
```

Once installed, the skills activate automatically when relevant, or you can
invoke one explicitly, e.g. `/proteomics:planning-proteomics-analysis`.

To update later, after new skills are pushed:

```
/plugin marketplace update proteomics-agent-skills
/plugin update proteomics@proteomics-agent-skills
```

## Skills

The `proteomics` plugin bundles the following skills:

| Skill | Purpose |
|-------|---------|
| `planning-proteomics-analysis` | Orchestrate an end-to-end analysis workflow |
| `reading-proteomics-data` | Read PSM tables / protein matrices and map columns to standard names |
| `performing-proteomics-quality-control` | Remove outlier samples and unsupported features |
| `normalizing-proteomics-data` | Assess the need for and apply intensity normalization |
| `imputing-proteomics-data` | Impute missing values for downstream analyses |
| `correcting-proteomics-batch-effects` | Diagnose and correct batch effects |
| `performing-statistical-analysis` | Differential expression analysis (t-tests, ANOVA, FDR, volcano plots) |
| `interpreting-biological-results` | ORA / GSEA, STRING / UniProt annotation |
| `formulating-biological-findings` | Draw biological and clinical conclusions |

## Repository layout

```
.claude-plugin/
  marketplace.json              # marketplace manifest
plugins/
  proteomics/
    .claude-plugin/plugin.json  # plugin manifest
    skills/
      <skill-name>/SKILL.md     # one directory per skill
```

## License

Apache-2.0 — see [LICENSE](LICENSE).
