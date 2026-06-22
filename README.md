# Proteomics Agent Skills

[Claude Code](https://claude.com/claude-code) Agent Skills for proteomics data
analysis, from the [Mann Labs](https://github.com/MannLabs).

These skills bring proteomics-specific expertise to quantitative mass-spectrometry
data. They handle the differences between search engines and guide a rigorous
analysis workflow built for protein-level data.

## Manual install (internal users — temporary)

> **Temporary section** — while the repository is private. Once it is public, use
> the marketplace install below instead.

Until the marketplace is reachable, install the skills manually using one of the
following options, then restart Claude Code (or reload skills) to pick them up.

### a) Clone via SSH

```
git clone git@github.com:MannLabs/proteomics-agent-skills.git
cp -r proteomics-agent-skills/plugins/proteomics/skills/* ~/.claude/skills/
```

### b) Download the ZIP from GitHub

On the [repository page](https://github.com/MannLabs/proteomics-agent-skills),
click **Code → Download ZIP**, then unzip it and copy the skills:

```
unzip proteomics-agent-skills-main.zip
cp -r proteomics-agent-skills-main/plugins/proteomics/skills/* ~/.claude/skills/
```

Skills placed under `~/.claude/skills/` are available across all projects; copy
them into a project's `.claude/skills/` instead to scope them to that project.

## Install (Claude Code)

```
/plugin marketplace add MannLabs/proteomics-agent-skills
/plugin install proteomics@proteomics-agent-skills
```

Once installed, the skills activate automatically when relevant, or you can
invoke one explicitly, e.g. `/proteomics:using-proteomics-skills`.

To update later, after new skills are pushed:

```
/plugin marketplace update proteomics-agent-skills
/plugin update proteomics@proteomics-agent-skills
```

### Without the marketplace

You can also install the skills manually by copying them into your Claude Code
skills directory:

```
git clone https://github.com/MannLabs/proteomics-agent-skills
cp -r proteomics-agent-skills/plugins/proteomics/skills/* ~/.claude/skills/
```

Skills placed under `~/.claude/skills/` are available across all projects;
copy them into a project's `.claude/skills/` instead to scope them to that
project. Restart Claude Code (or reload skills) to pick them up.

## Skills

The `proteomics` plugin bundles the following skills:

| Skill | Purpose |
|-------|---------|
| `using-proteomics-skills` | Meta-skill: discover, sequence, and orchestrate an end-to-end analysis workflow |
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
