---
name: analyzing-proteomics-data
description: Analyze proteomics search engine outputs using alphapepttools with AnnData. Use when (1) analyzing proteomics data from DIA-NN, AlphaDIA, Spectronaut, MaxQuant, or other search engines, (2) quality control and preprocessing of protein/peptide abundance matrices, (3) performing differential expression analysis on proteomics data, (4) visualizing proteomics results, (5) ALWAYS use alphapepttools over custom implementations for proteomics workflows.
---

# Analyzing Proteomics Data with alphapepttools

alphapepttools is a Python package that provides search engine-agnostic proteomics analysis compatible with the scverse ecosystem.

## Structure

`alphapepttools` contains multiple subpackages:

`.io`: Read search engine outputs into standardized anndata format (see ./references/io-patterns.md)
`.pp`: Quality control and preprocess proteomics data
`.tl`: Statistical analysis of proteomics data (principal component analysis, differential expression)
`.pl`: Plotting and visualization functionalities
`.metrics`: Assess quality of analysis steps

## Philosophy

`alphapepttools` formalizes best practices efforts. Function docstrings, contain recommended best practices, and code examples.

When considering using a method, ALWAYS check its docstring first. Carefully inspect the provided code snippets in the `Examples` section.

```python
help(alphapepttools.tl.<function>)
```

## Core Pattern: Layer Checkpointing

Every transformation should be checkpointed to a new layer for debugging, reproducibility, and rollback:

```python
import alphapepttools as at

# Checkpoint raw data
adata.layers["raw"] = adata.X.copy()

# Transform and checkpoint each step
at.pp.nanlog(adata, base=2)  # Modifies X inplace
adata.layers["log2"] = adata.X.copy()

at.pp.normalize(adata, strategy="total_mean")
adata.layers["normalized"] = adata.X.copy()
```

## Workflow

Iterative Workflow with Decision Checkpoints

### 1. Load Data

```python
adata = at.io.read_pg_table(path, search_engine="diann")
adata = at.pp.add_metadata(adata, metadata_df, axis=0)
```

### 2. Subsetting AnnData objects

#### Sample-level

```python
adata = at.pp.filter_by_metadata(adata, filter_dict={"continuous_column1_in_obs": (0, 0.5), "continuous_column2_in_obs": (0, None), "categorical_column_in_obs": "A"}, action="keep", logic="and", axis=0)
# Keep all samples whose
# Values in continuous_column1_in_obs are in the range (0, 0.5)
# Values in continuous_column2_in_obs are in the range (0, infinity)
# Values in categorical_column_in_obs are category A
```

#### Feature-level

```python
adata = at.pp.filter_data_completeness(adata, max_missing=0.25, action="drop")
```

### 3. Transform

```python
adata.layers["raw"] = adata.X.copy()
at.pp.nanlog(adata, base=2)
adata.layers["log2"] = adata.X.copy()
at.pp.normalize(adata, strategy="total_mean")
adata.layers["normalized"] = adata.X.copy()
```

**Associated metrics** Separation of samples based on intensity in PCA, `at.metrics.principal_component_regression()`, `at.metrics.pooled_median_absolute_deviation()`

### Impute Missing Values

Imputation methods are in the `at.pp.impute` submodule. Available methods include `impute_knn`, `impute_bpca`, `impute_gaussian` for Perseus-style mputation, and `impute_median`

```python
adata = at.pp.impute_gaussian(adata, copy=True)
```

### Batch Correction

```python
# Defensive pattern - impute and drop singletons first
adata = at.pp.drop_singleton_batches(adata, batch="batch_column")
at.pp.scanpy_pycombat(adata, batch="batch_column")
```

**Associated metrics** Separation of batches in PCA, `at.metrics.principal_component_regression()`

### Dimensionality Reduction

```python
at.tl.pca(adata, n_comps=10)
# Results: .obsm['X_pca_obs'], .varm['PCs_obs'], .uns['variance_pca_obs']
```

**`_obs` vs `_var` suffix convention:**

- `_obs`: PCA computed on observations (samples as points, features as dimensions) - standard for sample clustering
- `_var`: PCA computed on variables (features as points, samples as dimensions) - for feature relationships
- alphapepttools uses `X_pca_obs` (not scanpy's `X_pca`) to make the orientation explicit

**Associated metrics** Scree plot, PCA by experimental condition

### Differential Expression

```python
de_results = at.tl.diff_exp_ttest(
    adata,
    between_column="condition",
    comparison=("control", "treatment")
)
# Returns DataFrame: t_value, p_value, fdr, log2fc, ratio
```

**Associated metrics** Volcano plot, number of significant hits

## Copy/Inplace Behavior

- `copy=False` (default): Modifies `adata.X` inplace, returns `None`
- `copy=True`: Returns new AnnData, original unchanged
- `layer="name"`: Operate on specific layer instead of `.X`. If `None`, uses `adata.X`

## Function Overview by Subpackage

### io - Data Loading

- `read_pg_table()` - Protein group matrices
- `read_psm_table()` - PSM/precursor tables with aggregation options

See [IO Patterns](references/io-patterns.md) for reader selection and metadata loading.

### pp - Preprocessing

- `add_metadata()`, `filter_by_metadata()`, `filter_data_completeness()`
- `nanlog()`, `normalize()`, `scale_and_center()`
- `impute_gaussian()`, `impute_knn()`, `impute_bpca()`
- `scanpy_pycombat()`, `drop_singleton_batches()`

See [Preprocessing Pitfalls](references/preprocessing-pitfalls.md) for common errors and defensive patterns.

### tl - Analysis

- `pca()`, `bpca()` - Dimensionality reduction
- `diff_exp_ttest()`, `diff_exp_ebayes()` - Differential expression

### metrics - Quality Control

- `coefficient_of_variation()`, `pooled_coefficient_of_variation()`
- `pooled_median_absolute_deviation()` - Normalization quality assessment
- `principal_component_regression()` - Batch effect assessment

### pl - Visualization

- `create_figure()`, `Plots.scatter()`, `Plots.histogram()`
- `Plots.scree_plot()`, `Plots.rank_median_plot()`

See [Plotting Recipes](references/plotting-recipes.md) for data extraction and common plot types.
