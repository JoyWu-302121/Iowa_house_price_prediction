# Iowa House Price Prediction

本專案使用 Iowa 房價資料，以指定的 47 個 features 建立可重現的 EDA 與線性模型流程。正式建模 target 為 `log1p(SalePrice)`，並比較 OLS、Ridge 與 Lasso。

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
- 47 regressors：21 numerical features、1 ordinal `BsmtQual`、25 `Neighborhood` indicators
- Split：1,800 training、600 validation、508 test observations
- Target：`log1p(SalePrice)`
- Models：OLS、Ridge（α = 0.10、0.30、0.60）、Lasso（α = 0.02、0.06、0.10）
- Primary metrics：MSE、RMSE、MAE、R²，均在 log-price scale 計算
- Model selection：只使用 validation MSE；test set 只評估選定的 final model

## Setup

需要 Python 3.11。下載 repository 後，在專案根目錄執行：

```bash
conda create -n ML python=3.11 -y
conda activate ML
python -m pip install -r requirements.txt
python -m ipykernel install --user --name ML --display-name "Python (ML)"
```

啟動 Jupyter：

```bash
jupyter lab
```

若使用 VS Code，開啟 notebook 後選擇 `Python (ML)` kernel。

## Run order

1. 執行 `notebooks/01_eda.ipynb`：資料檢查、47-feature construction、單變數分析、target distribution 與 correlation heatmap。
2. 執行 `notebooks/02_modeling.ipynb`：資料切分、preprocessing、候選模型比較、final test evaluation 與 coefficient analysis。

兩份 notebook 都會從 `data/IA_House_Price_Original_Data.xlsx` 載入資料，並支援從 repository root 或 `notebooks/` 目錄啟動。

## Current result

Validation MSE 最低的模型為 Ridge（α = 0.60）。目前 final test results（log-price scale）：

| Metric | Value |
|---|---:|
| MSE | 0.0145 |
| RMSE | 0.1204 |
| MAE | 0.0865 |
| R² | 0.9083 |

美元尺度的輔助結果：test RMSE 約 `$21,558.74`，test MAE 約 `$15,045.23`。

## Reproducibility notes

- `StandardScaler` 僅在 training partition 上 fit，避免 data leakage。
- 固定 random seed `20260928`，確保資料切分可重現。
- Excel 原始表頭位於第 4 列，因此 notebook 使用 `skiprows=3`。
- `BsmtFinSF = BsmtFinSF1 + BsmtFinSF2`。
- `BsmtQual` 映射為 `Ex=5, Gd=4, TA=3, Fa=2, Po=1, NA=0`。

