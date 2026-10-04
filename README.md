# Iowa House Price Prediction

This project uses the Iowa house-price dataset to build a reproducible exploratory data analysis and linear-modeling workflow with a specified set of 47 features. The modeling target is `log1p(SalePrice)`, and the candidate models are OLS, Ridge, and Lasso.

## Repository structure

```text
Iowa_House_Price_Prediction/
├── data/
│   └── IA_House_Price_Original_Data.xlsx
├── notebooks/
│   ├── 01_eda.ipynb
│   └── 02_modeling.ipynb
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

## Reproducibility notes

- `StandardScaler` is fitted only on the relevant training partition to prevent data leakage.
- The fixed random seed `20260928` makes the data split reproducible.
- The Excel header begins on the fourth row, so the notebooks use `skiprows=3`.
- `BsmtFinSF = BsmtFinSF1 + BsmtFinSF2`.
- `BsmtQual` is mapped as `Ex=5, Gd=4, TA=3, Fa=2, Po=1, NA=0`.
