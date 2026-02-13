# Preprocessing Pitfalls

## Processing Order

The canonical order matters:

```
filter → log → normalize → impute → batch correct
```

**Why this order:**

1. Filter first to remove low-quality features before transformation
2. Log-transform to stabilize variance and enable additive statistics
3. Normalize to make samples comparable
4. Impute after normalization so imputed values are on the same scale
5. Batch correct last - requires complete data (no NaN)

## Layer Checkpointing

Checkpoint each transformation step for debugging and rollback:

```python
# Checkpoint raw intensities
adata.layers["raw"] = adata.X.copy()

# Log transform (inplace)
at.pp.nanlog(adata, base=2)
adata.layers["log2"] = adata.X.copy()

# Normalize (inplace)
at.pp.normalize(adata, strategy="total_mean")
adata.layers["normalized"] = adata.X.copy()

# Impute (inplace)
at.pp.impute_gaussian(adata)
adata.layers["imputed"] = adata.X.copy()
```

To revert or compare:

```python
# Restore to a previous state
adata.X = adata.layers["normalized"].copy()

# Compare distributions before/after
adata.layers["log2"][:, 0]  # Before normalization
adata.layers["normalized"][:, 0]  # After normalization
```

## Batch Correction Requirements

`scanpy_pycombat()` has two failure modes:

### 1. NaN Values

ComBat cannot handle missing data. Always impute first:

```python
# Impute before batch correction
at.pp.impute_gaussian(adata)
adata.layers["imputed"] = adata.X.copy()

at.pp.scanpy_pycombat(adata, batch="batch")
adata.layers["batch_corrected"] = adata.X.copy()
```

### 2. Singleton Batches

Batches with only one sample cause errors. Drop them:

```python
adata = at.pp.drop_singleton_batches(adata, batch="batch")
at.pp.scanpy_pycombat(adata, batch="batch")
```

## Imputation Strategy Selection

### MNAR (Missing Not At Random)

```python
adata.layers["pre_impute"] = adata.X.copy()

# Perseus-style: shift 1.8σ down, width 0.3σ
at.pp.impute_gaussian(adata)

# Group-wise when groups have different abundance profiles
at.pp.impute_gaussian(adata, group_column="cell_type")

adata.layers["imputed"] = adata.X.copy()
```

### MAR (Missing At Random)

- Symptom: Missing values distributed randomly across abundance range
- Cause: Technical dropout, sampling variation
- Solution: `impute_knn()` or `impute_bpca()`

```python
adata.layers["pre_impute"] = adata.X.copy()

at.pp.impute_knn(adata, n_neighbors=5)
# OR
at.pp.impute_bpca(adata)

adata.layers["imputed"] = adata.X.copy()
```

## Common Filter Mistakes

### Over-filtering

```python
# Start with a more permissive threshold
adata = at.pp.filter_data_completeness(adata, max_missing=0.3)
```

### Filter After Imputation

```python
# CORRECT - filter before imputation
adata = at.pp.filter_data_completeness(adata, max_missing=0.3)

adata.layers["pre_impute"] = adata.X.copy()
at.pp.impute_gaussian(adata)
adata.layers["imputed"] = adata.X.copy()
```

## Copy Semantics

Most functions default to `copy=False` (inplace modification):

```python
# Inplace (returns None, modifies adata.X)
at.pp.normalize(adata, strategy="total_mean")

# Returns new AnnData (original unchanged)
adata_new = at.pp.normalize(adata, strategy="total_mean", copy=True)
```

**When to use `copy=True`:**

- Comparing different parameter choices
- Branching workflows (e.g., try gaussian vs knn imputation)

**When to use inplace + layers:**

- Linear workflows where you checkpoint each step
- Saves memory (no duplicate AnnData objects)
