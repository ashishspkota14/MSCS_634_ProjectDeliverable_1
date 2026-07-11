# MSCS-634 Project Deliverable 1: Data Collection, Cleaning, and Exploration

## Dataset Summary
This project uses a historical weather dataset from Szeged, Hungary (sourced from
Kaggle), containing 96,453 hourly observations across 12 attributes including
temperature, humidity, wind speed, pressure, and precipitation type. The dataset
was selected for its realistic mix of numeric and categorical features, genuine
data quality issues, and a natural continuous target (Temperature) suited to
regression modeling in later project phases.

## Key Insights
- Humidity has a strong negative correlation with Temperature (r ≈ -0.64), making it
  the most important predictive feature identified during EDA.
- Wind Speed contains a substantial number of high-value outliers requiring treatment
  before use in modeling.
- Precipitation Type is heavily imbalanced (rain far more common than snow).
- A cluster of physically impossible zero-pressure readings was identified and
  removed as sensor noise.

## Major Steps Taken
1. Loaded the dataset and inspected its structure (96,453 rows, 12 columns).
2. Checked for and confirmed zero exact duplicate rows.
3. Handled 517 missing values in Precip Type using mode imputation.
4. Identified and removed rows with erroneous zero-value Pressure readings (sensor
   noise).
5. Dropped two low-value columns (Loud Cover — a constant; Daily Summary — free text)
   that added no value to numerical analysis.
6. Conducted EDA using histograms, scatter plots, box plots, a correlation heatmap,
   and a categorical distribution chart to explore feature distributions,
   relationships, and outliers.

## Challenges and How They Were Addressed
- Precip Type's missing values needed a defensible imputation strategy; mode
  imputation was chosen since it is a categorical column with one dominant class.
- Distinguishing genuine extreme weather readings from sensor error required domain
  reasoning — zero-millibar pressure readings are physically implausible at sea
  level, so these were treated as noise and removed, while legitimate outliers in
  Wind Speed were flagged for treatment in later modeling rather than removed
  outright at this stage.
- Balancing thoroughness with scope: EDA covered distributions, relationships, and
  outliers across the dataset's key numeric and categorical features without
  attempting to visualize every possible feature combination.
