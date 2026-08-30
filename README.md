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
- One-hot encoded categorical columns (`StoreType`, `Assortment`,
  `StateHoliday`, `PromoInterval`) into numeric 0/1 columns.

### 5. Modeling
- Split data chronologically (train on data before 2015-06-01, test on
  everything after) to simulate real deployment — never randomly, to
  avoid leaking future information into training.
- Built a naive baseline ("sales same day last week") to benchmark
  against.
- Trained and compared Linear Regression, Random Forest, and XGBoost.

### 6. Evaluation
- Evaluated every model on the same held-out test period using RMSE,
  MAE, MAPE, and R².
- XGBoost was the best performer, though the improvement over Random
  Forest was modest — most of the gain came from moving past a purely
  linear model to one that captures non-linear feature interactions.
- Feature importance (from Random Forest) showed `Sales_RollingMean_7`
  (62%) and `Promo` (16%) as by far the most influential features —
  quantitatively confirming the EDA findings above. The two engineered
  time-series features together account for ~64% of the model's
  decision-making, underlining that feature engineering mattered more
  than model choice for this problem.

## Results

| Model | RMSE | MAE | MAPE | R² |
|-------|------|-----|------|-----|
| Naive Baseline | 3063.02 | 2433.95 | 36.94% | 0.0307 |
| Linear Regression | 1542.51 | 1107.97 | 16.89% | 0.7542 |
| Random Forest | 1158.68 | 810.89 | 11.91% | 0.8613 |
| **XGBoost** | **1084.53** | **766.83** | **11.20%** | **0.8785** |

XGBoost reduced RMSE by ~65% and improved R² from 0.03 to 0.88 compared
to the naive baseline.

## Live Demo
_(Add Streamlit link once deployed)_

## What I'd do next
- Investigate the gap between RMSE and MAE further with targeted error
  analysis — likely concentrated around holidays and unusual promo days.
- Try time-series cross-validation instead of a single train/test split
  for a more robust performance estimate.
- Experiment with additional lag windows (e.g. 14-day, 30-day) and
  holiday-proximity features.

## How to run
```bash
pip install -r requirements.txt
jupyter notebook notebooks/exploration.ipynb
```

Note: `data/raw_data/` and `data/processed_data/` are excluded from this
repo (see `.gitignore`) since they're either downloadable from Kaggle or
regenerable by running the notebook. See the Dataset section above for
where to get the raw files.