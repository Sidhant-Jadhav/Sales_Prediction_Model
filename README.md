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

### 1. Data Loading & Merging
Combined daily sales records (`train.csv`) with store metadata (`store.csv`)
on `Store` ID into a single dataset — 1,017,209 rows, 18 columns.

### 2. Exploratory Data Analysis
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

### 3. Data Cleaning
- Removed 172,817 rows where stores were closed (`Open=0`) — no useful
  signal for prediction.
- Fixed a mixed-type issue in `StateHoliday` by loading it explicitly
  as a string.
- Filled missing values with strategies matched to what "missing"
  actually meant per column:
  - `CompetitionDistance` (2,642 missing) → filled with median distance.
  - `CompetitionOpenSinceMonth`/`Year` (~268k missing) → filled with 0
    (flag for unknown/no competition).
  - `Promo2SinceWeek`/`Year`/`PromoInterval` (~508k missing) → filled
    with 0 / 'None' (store not enrolled in Promo2).
- Final cleaned shape: 844,392 rows, 18 columns.

### 4. Feature Engineering
- Extracted `Year`, `Month`, `Day`, `WeekOfYear` from the date column.
- Added an `IsWeekend` flag (Saturday/Sunday).
- Built `Sales_Lag_7` — sales at the same store 7 rows (~1 week) earlier,
  capturing weekly seasonality.
- Built `Sales_RollingMean_7` — average sales over the trailing 7 days
  (excluding the current day), capturing recent trend/momentum.
- Dropped rows where lag/rolling features couldn't be computed (start
  of each store's history).
- Final feature set: 836,587 rows, 25 columns.

### 5. Modeling _(in progress)_
- Baseline (naive) vs Linear Regression vs Random Forest vs XGBoost

### 6. Evaluation _(planned)_
- Time-series cross-validation, error analysis

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

Note: `data/raw_data/` and `data/processed_data/` are excluded from this
repo (see `.gitignore`) since they're either downloadable from Kaggle or
regenerable by running the notebook. See the Dataset section above for
where to get the raw files.