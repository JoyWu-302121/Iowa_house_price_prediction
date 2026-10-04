# Iowa House Price Prediction

This project uses the Iowa house-price dataset to build a reproducible exploratory data analysis and linear-modeling workflow with a specified set of 47 features. The modeling target is `log1p(SalePrice)`, and the candidate models are OLS, Ridge, and Lasso.

## Repository structure

```text
Iowa_House_Price_Prediction/
├── data/
│   └── IA_House_Price_Original_Data.xlsx
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_modeling.ipynb
│   └── 02_modeling_new.ipynb
├── output/
│   └── pdf/
│       ├── Iowa_House_Price_Report_WZZ.pdf
│       └── Iowa_House_Price_Overleaf.zip
├── README.md
└── requirements.txt
```

## Modeling specification

- 2,908 observations
- 47 regressors: 21 numerical features, one ordinal `BsmtQual` feature, and 25 `Neighborhood` indicators
- Split: 1,800 training, 600 validation, and 508 test observations
- Target: `log1p(SalePrice)`
- Models: OLS, Ridge (α = 0.10, 0.30, 0.60), and Lasso (α = 0.02, 0.06, 0.10)
- Primary metrics: MSE, RMSE, MAE, and R², all calculated on the log-price scale
- Model selection: validation MSE only; the test set is used once for the selected final model

## Setup

Python 3.11 is required. After cloning the repository, run the following commands from the project root:

```bash
conda create -n ML python=3.11 -y
conda activate ML
python -m pip install -r requirements.txt
python -m ipykernel install --user --name ML --display-name "Python (ML)"
```

Start JupyterLab:

```bash
jupyter lab
```

If you use VS Code, open a notebook and select the `Python (ML)` kernel.

## Run order

1. Run `notebooks/01_eda.ipynb` for data validation, 47-feature construction, univariate analysis against `SalePrice`, the target distribution, and the correlation heatmap.
2. Run `notebooks/02_modeling.ipynb` for data splitting, preprocessing, candidate-model comparison, final test evaluation, and coefficient analysis.

Both notebooks load `data/IA_House_Price_Original_Data.xlsx` and support execution from either the repository root or the `notebooks/` directory.

## Current result

OLS has the lowest validation MSE (0.014917). Ridge with α = 0.60 is nearly tied, with a validation MSE of 0.014929. Following the predefined model-selection rule, OLS is selected as the final model.
The final test results on the log-price scale are:

| Metric | Value |
|---|---:|
| MSE | 0.0144 |
| RMSE | 0.1201 |
| MAE | 0.0862 |
| R² | 0.9087 |

For additional interpretation on the dollar scale, the test RMSE is approximately $21,545.45, and the test MAE is approximately $14,996.77.

Because the 47-feature design contains exact linear dependencies, the individual OLS coefficients are not uniquely identified. The OLS prediction metrics remain valid, while Ridge with α = 0.60 is used as a stable reference for coefficient interpretation.

The optional application uses 124 N Franklin Ave, Ames, IA 50014. This property is particularly useful because its April 2008 sale appears as an exact observation in the supplied dataset and belongs to the held-out test set under random seed `20260928`.

Using the original 2008 feature values:

| Value | Amount |
|---|---:|
| Actual 2008 sale price | $119,000 |
| OLS prediction | $123,195 |
| Ridge prediction (α = 0.60) | $122,874 |

The OLS historical prediction is 3.53% above the actual sale price, while the Ridge prediction is 3.26% above it.

After updating the available features to match the current listing:

| Value | Amount |
|---|---:|
| Current-feature OLS prediction | $121,869 |
| Current-feature Ridge prediction (α = 0.60) | $121,451 |
| 2026 listing price | $246,500 |
| Zillow Zestimate | $242,000 |

The historical model performs well on the property's 2008 sale but substantially underpredicts its 2026 market value. The model does not include sale year, inflation, or a housing-price index, so this difference illustrates temporal distribution shift rather than simply a poor historical prediction.

## Reproducibility notes

- `StandardScaler` is fitted only on the relevant training partition to prevent data leakage.
- The fixed random seed `20260928` makes the data split reproducible.
- The Excel header begins on the fourth row, so the notebooks use `skiprows=3`.
- `BsmtFinSF = BsmtFinSF1 + BsmtFinSF2`.
- `BsmtQual` is mapped as `Ex=5, Gd=4, TA=3, Fa=2, Po=1, NA=0`.
