# Plotting Recipes

All alphapepttools plots use on matplotlib axes that are passed to the `ax` argument to the plotting functions.
The `ax` object can be further modified with standard matplotlib syntax.

## Figure Creation Pattern

```python
fig, axm = at.pl.create_figure(n_rows, n_cols, figsize=(width, height))
ax = axm.next()  # Get next subplot
at.pl.Plots.scatter(ax=ax, data=adata, ...)
```

The `AxesManager` (`axm`) is a generator - call `.next()` to move through subplots row-wise. You can also index directly: `axm[1, 0]`.
The generator returns

## PCA Plots

### Direct PCA Plot (preferred)

Plot PCA directly from AnnData without manual extraction:

```python
fig, axm = at.pl.create_figure(1, 2)

# Basic PCA colored by metadata
at.pl.Plots.plot_pca(
    data=adata,
    ax=axm.next(),
    x_column=1,           # PC1
    y_column=2,           # PC2
    color_map_column="condition",
    legend="auto"
)

# Color by protein expression
at.pl.Plots.plot_pca(
    data=adata,
    ax=axm.next(),
    x_column=1,
    y_column=2,
    color_map_column="Q07021",  # Protein ID from adata.var
    palette=BaseColormaps.get("sequential")
)
```

### PCA Loadings

```python
fig, axm = at.pl.create_figure(1, 2)

# Top loadings for one PC (bar plot)
at.pl.Plots.plot_pca_loadings(data=adata, ax=axm.next(), dim=1, nfeatures=20)

# 2D loading plot with labels
at.pl.Plots.plot_pca_loadings_2d(
    data=adata,
    ax=axm.next(),
    pc_x=1,
    pc_y=2,
    nfeatures=20,
    add_labels=True,
    add_lines=True
)
```

### Scree Plot

```python
at.pl.Plots.scree_plot(ax=ax, adata=adata, n_pcs=10)
```

### Manual PCA Data Extraction

When you need a DataFrame (e.g., for custom plots):

```python
pca_df = pd.DataFrame(
    adata.obsm["X_pca_obs"],
    index=adata.obs.index,
    columns=[f"PC_{i+1}" for i in range(adata.obsm["X_pca_obs"].shape[1])]
)
pca_df = pca_df.join(adata.obs)

# Variance explained
adata.uns["variance_pca_obs"]["variance_ratio"]
```

## Volcano Plot with Label Anchoring

`label_plot` uses x-axis anchors to stack labels neatly:

```python
fig, axm = at.pl.create_figure(1, 1)
ax = axm.next()

# Plot points
at.pl.Plots.scatter(ax=ax, data=de_results, x_column="log2fc", y_column="neg_log10pval")
```

## Distribution Plots

Violin, box, and bar plots support two modes:

### Long Format (grouping column + value column)

```python
# Compare one protein across conditions
at.pl.Plots.violinplot(
    ax=ax,
    data=adata,
    grouping_column="condition",
    value_column="P12345"  # Can be from X or obs
)

at.pl.Plots.boxplot(ax=ax, data=adata, grouping_column="batch", value_column="total_intensity")
at.pl.Plots.barplot(ax=ax, data=adata, grouping_column="condition", value_column="protein_count")
```

### Wide Format (multiple columns directly)

```python
# Compare multiple proteins side-by-side
at.pl.Plots.violinplot(
    ax=ax,
    data=adata,
    direct_columns=["P12345", "P67890", "Q11111"]
)
```

## Histogram

```python
# Single color
at.pl.Plots.histogram(ax=ax, data=adata, value_column="intensity", bins=20)

# Colored by groups with styling
at.pl.Plots.histogram(
    ax=ax,
    data=adata,
    value_column="intensity",
    color_map_column="condition",
    bins=20,
    legend="auto",
    hist_kwargs={"alpha": 0.7, "histtype": "stepfilled", "edgecolor": "black"}
)
```

## Save Figures

```python
at.pl.save_figure(fig, filename="figure.svg", output_dir="./outputs", dpi=300)
```
