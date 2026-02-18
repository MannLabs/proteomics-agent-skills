# IO Patterns

## Reader Selection

| Data Source          | Function                 | When to Use                                                          |
| -------------------- | ------------------------ | -------------------------------------------------------------------- |
| Protein group matrix | `at.io.read_pg_table()`  | Pre-aggregated protein quantities (most common)                      |
| PSM/precursor table  | `at.io.read_psm_table()` | Need peptide-level analysis of protein group matrix is not available |

## Supported Search Engines

```python
at.io.list_available_reader()  # Returns supported engines
```

Engines: `diann`, `alphadia`, `spectronaut`, `maxquant`, `alphapept`, `msfragger`, `sage`

## Feature Level Selection (PSM tables only)

```python
adata = at.io.read_psm_table(
    paths,
    search_engine="diann",
    feature_level="proteins"  # "proteins" | "precursors" | "peptides" | "genes"
)
```

## Loading with Metadata

```python
# 1. Load proteomics data
adata = at.io.read_pg_table(path, search_engine="diann")

# 2. Prepare metadata (index must match adata.obs.index)
metadata = pd.read_csv("metadata.csv").set_index("sample_id")

# 3. Join metadata to AnnData
adata = at.pp.add_metadata(
    adata,
    metadata,
    axis=0,              # 0=samples (obs), 1=features (var)
    keep_data_shape=False  # Drop samples without metadata
)
```

## Index Alignment

`add_metadata()` enforces index matching (not just shape compatibility). Order differences are handled automatically - correct metadata rows are matched to correct samples.

## Quick DataFrame Export

```python
# Full matrix with sample metadata
df = adata.to_df().join(adata.obs)
```
