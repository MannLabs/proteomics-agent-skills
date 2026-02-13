# Decoupler Usage Reference

## Setup

Get a prior knowledge source as network

```python
import decoupler as dc

# Show all resources
dc.op.show_resources()

# Get MSigDB meta resource
resource = dc.op.resource("MSigDB", organism='human', license='academic', verbose=False)

# Get all reactome pathways as network
network = resource.loc[resource["collection"]=="reactome_pathways"]

# The network has to preprocessed
# Decoupler expects genesets as column with the name `source` and genes as `target`
# Networks might contain duplicates which need to be removed
net = (
    net
    .rename(columns={"geneset": "source", "genesymbol": "target"})
    .drop_duplicates(subset=("source", "target"))
)
```

## Tool call

Run ORA or GSEA (mininum geneset [target] size: 3)

```python
# Takes wide tables [pandas dataframe or anndata] (samples x features) and returns wide tables (sample x geneset)
scores, pvals = dc.mt.ora(adata, net=resource, tmin=3)
scores, normalized_scors, pvals = dc.mt.gsea(adata, net, tmin=3)
```

## Postprocessing

1. Use Benjamini-Hochberg correction on the returned p-values
2. Melt the results into long format
3. Join scores with adjusted p-values on genesets and samples as keys

```python
from scipy.stats import false_discovery_control
# Control FDR per sample
fdr = false_discovery_control(pvals, axis=0)
fdr = pd.DataFrame(fdr, columns=pvals.columns, index=pvals.index)


# Melt dataframes
scores_long = (
    scores
    .melt(ignore_index=False, var_name="geneset", value_name="score")
    .reset_index(names="sample")
)

fdr_long = (
    fdr
    .melt(ignore_index=False, var_name="geneset", value_name="fdr")
    .reset_index(names="sample")
)

enrichment_results = pd.merge(
    scores_long, fdr_long, on=("sample", "geneset")
)
```
