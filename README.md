# Sales Prediction Model

## Problem Statement
Retail store managers need to plan inventory and staffing ahead of time, but
sales fluctuate due to promotions, holidays, and seasonality. This project
builds a model to forecast daily store sales, helping managers plan ahead
and reduce stockout/overstock risk.

## Dataset
[Rossmann Store Sales](https://www.kaggle.com/c/rossmann-store-sales) (Kaggle)
— daily sales for 1,115 stores over ~2.5 years, including promotions,
holidays, and store metadata.

To run this project, download `train.csv` and `store.csv` from the link
above and place them in `data/raw_data/`.

## Approach
- **Data loading & merging**: Combined daily sales records with store
  metadata (store type, assortment, competition info) into a single dataset.
- **Exploratory Data Analysis**:
  - Sales range from 0 to ~41,551, with high day-to-day variability
    (mean ~5,774, std ~3,850).
  - Found 172,817 rows with zero sales tied to closed stores (`Open=0`) —
    expected. Also found 54 anomalous rows with zero sales despite the
    store being marked open.
  - Sales show strong weekly seasonality: Sunday sales are near-zero
    (most stores closed), Monday has the highest median sales.
  - Sales show sharp holiday-driven drops but no long-term trend across
    the 2.5-year period.
  - Promotions have a large effect: average sales are ~81% higher on
    promo days (7,991) vs non-promo days (4,406).
  - Individual store patterns (e.g. Store 1) mirror the aggregate,
    confirming these patterns generalize across stores.
- **Data cleaning**: _(in progress)_
- **Feature engineering**: _(planned)_
- **Modeling**: baseline vs Linear Regression vs Random Forest vs XGBoost _(planned)_
- **Evaluation**: time-series cross-validation, error analysis _(planned)_

## Results
_(Fill in once model comparison is complete)_

| Model | RMSE | MAPE |
|-------|------|------|
| Naive Baseline | - | - |
| Linear Regression | - | - |
| Random Forest | - | - |
| XGBoost | - | - |

## Live Demo
_(Add Streamlit link once deployed)_

## What I'd do next
_(Fill in at the end)_

## How to run
```bash
pip install -r requirements.txt
jupyter notebook notebooks/exploration.ipynb
```
