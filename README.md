# MSCS-634 Project Deliverable 1: Data Collection, Cleaning, and Exploration

## Overview

This deliverable is the first phase of a multi-part project applying data mining
techniques to a real-world weather dataset. The goal of this phase is to select an
appropriate dataset, load and inspect it, clean it into a reliable analytical form,
and conduct exploratory data analysis (EDA) to surface patterns that will guide
regression, classification, and clustering work in later project deliverables.

## Dataset Description

**Source:** Historical Weather — Szeged, Hungary (Kaggle: budincsevity/szeged-weather)

**Size:** 96,453 hourly observations, 12 original attributes

**Attributes:**
| Column | Type | Description |
|---|---|---|
| Formatted Date | datetime | Timestamp of the observation |
| Summary | categorical | Short weather description |
| Precip Type | categorical | Precipitation type (rain/snow) |
| Temperature (C) | numeric | Air temperature |
| Apparent Temperature (C) | numeric | "Feels like" temperature |
| Humidity | numeric | Relative humidity (0–1 scale) |
| Wind Speed (km/h) | numeric | Wind speed |
| Wind Bearing (degrees) | numeric | Wind direction |
| Visibility (km) | numeric | Visibility distance |
| Loud Cover | numeric | Constant column (always 0) |
| Pressure (millibars) | numeric | Atmospheric pressure |
| Daily Summary | text | Free-text daily description |

**Why this dataset was selected:** It comfortably exceeds typical minimum size
requirements, mixes numeric and categorical attribute types, contains genuine
real-world data quality problems (missing values, duplicates, sensor noise, class
imbalance), and includes Temperature — a continuous variable with strong,
physically meaningful relationships to other features — making it well suited to
the regression modeling required in Project Deliverable 2.

## Methodology

### 1. Data Loading and Initial Inspection
The dataset was loaded into a Pandas DataFrame, the Formatted Date column was
converted to a proper datetime type, and rows were sorted chronologically.
`df.info()` confirmed the full 96,453 x 12 structure and each column's data type.

### 2. Data Quality Assessment
- **Duplicate check:** `df.duplicated().sum()` identified **24 exact duplicate rows**.
- **Missing value check:** `df.isnull().sum()` identified **517 missing values**,
  all concentrated in the Precip Type column; every other column was fully populated.
- **Noise check:** Numeric ranges were inspected for physically implausible values.
  Humidity's range (0.0-1.0) was valid, but Pressure's minimum was exactly **0.0
  millibars** — physically impossible at sea level — affecting **1,288 rows**.

### 3. Data Cleaning
- **Missing values:** The 517 missing Precip Type entries were filled using mode
  imputation (the dominant class, "rain"), since dropping these rows outright would
  discard otherwise-complete records for a relatively small (~0.5%) gap.
- **Noisy data:** The 1,288 rows with erroneous zero-value Pressure readings were
  removed, reducing the dataset from 96,453 to **95,165 rows**.
- **Low-value columns:** "Loud Cover" (a constant, always 0) and "Daily Summary"
  (unstructured free text) were dropped, reducing the column count from 12 to **10**.
- **Duplicates:** The 24 exact duplicate rows identified during inspection were
  documented; given their small number relative to the dataset (0.03%), they do not
  materially affect downstream statistics and were retained for transparency,
  though they can be trivially dropped with `df.drop_duplicates()` if a future
  deliverable requires a strictly duplicate-free dataset.

**Final cleaned dataset shape: 95,165 rows x 10 columns.**

### 4. Exploratory Data Analysis
Five visualizations were produced to explore distributions, relationships, and
outliers:
1. **Histogram of Temperature (C)** — distribution shape and spread
2. **Scatter plot (Humidity vs. Temperature)** — bivariate relationship
3. **Box plot (Wind Speed)** — outlier detection
4. **Correlation heatmap (6 numeric features)** — pairwise linear relationships
5. **Bar chart (Precip Type)** — categorical class balance

## Key Insights

- **Temperature distribution:** Roughly bell-shaped with a mild spread toward
  warmer values, indicating it is well suited to linear modeling techniques without
  requiring a transformation.
- **Humidity-Temperature relationship:** The strongest relationship in the dataset
  (r is approximately -0.63 to -0.64) — as humidity rises, temperature falls. This
  makes Humidity the leading candidate predictor for Temperature in Deliverable 2's
  regression models.
- **Visibility-Temperature relationship:** A moderate positive correlation
  (r is approximately 0.39), and Visibility also correlates negatively with
  Humidity (r is approximately -0.37), suggesting visibility could serve as a
  secondary predictive feature.
- **Wind Speed outliers:** A box plot shows a long tail of high-speed readings well
  above the interquartile range, extending past 60 km/h. These should be addressed
  via IQR-based filtering before use in regression or distance-based models.
- **Precip Type imbalance:** "Rain" accounts for roughly 85,000 records versus
  roughly 10,000 for "snow" — a meaningful class imbalance to account for in any
  future classification modeling using this feature.
- **Sensor noise in Pressure:** A cluster of 1,288 physically impossible zero
  readings was identified and removed, preventing these errors from distorting
  summary statistics and regression coefficients downstream.

## How These Insights Guide Future Modeling

These findings directly shape the approach for Project Deliverable 2 (Regression
Modeling): Humidity, Wind Speed, and Precip Type emerge as strong candidate features
for predicting Temperature, while the outlier and noise-handling performed here
ensures that later models are trained on a dataset free of sensor artifacts that
would otherwise bias coefficient estimates. The Precip Type class imbalance is also
noted for consideration in Deliverable 3's classification work.

## Challenges and How They Were Addressed

- **Choosing an imputation strategy for Precip Type:** Because it is categorical
  with a dominant class, mode imputation was selected over row deletion to avoid
  losing otherwise-complete records for a relatively small (~0.5%) gap.
- **Distinguishing genuine extreme readings from sensor error:** Zero-millibar
  pressure is physically implausible at sea level, so these 1,288 rows were treated
  as noise and removed — a domain-reasoning judgment call rather than a purely
  statistical one.
- **Deciding how to handle exact duplicates:** With only 24 duplicate rows out of
  96,453 (0.03%), the impact on aggregate statistics is negligible; they were
  documented rather than silently dropped, keeping the cleaning process transparent
  and auditable.
- **Balancing EDA scope:** Five visualizations were chosen to cover distribution
  shape, bivariate relationships, outlier detection, multivariate correlation, and
  categorical balance — a representative cross-section of the dataset without
  attempting to visualize every possible feature combination.

## Repository Contents
- `MSCS_634_ProjectDeliverable_1.ipynb` — full notebook with code, comments, and
  visualizations
- `weatherHistory.csv` — source dataset
- `README.md` — this file

## How to Run
1. Clone this repository
2. Install dependencies: `pip install pandas numpy matplotlib seaborn`
3. Open `MSCS_634_ProjectDeliverable_1.ipynb` in Jupyter Notebook and run all cells
