# Exploratory Data Analysis package

`EDA.tex` is a standalone LaTeX document containing the exploratory analysis used
in the Iowa house-price report. The five PNG figures are direct extractions of the
saved outputs in `notebooks/01_eda.ipynb`; they were not redrawn or recomputed.

The document covers:

- SalePrice and `log1p(SalePrice)` distributions;
- continuous, count, and ordinal single-variable analyses;
- neighborhood median sale prices with sample sizes; and
- a Pearson-correlation heatmap for the 13 continuous regressors.

Compile `EDA.tex` with pdfLaTeX from this directory. The document expects the
included `figures/` folder to remain alongside `EDA.tex`.
