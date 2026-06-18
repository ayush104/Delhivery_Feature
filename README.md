# 🚚 Delhivery — Feature Engineering & EDA Case Study

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas)
![Scipy](https://img.shields.io/badge/Scipy-Hypothesis%20Testing-8CAAE6?logo=scipy)
![Sklearn](https://img.shields.io/badge/Sklearn-Normalization-F7931E?logo=scikit-learn)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## Overview

This project performs end-to-end **feature engineering and exploratory data analysis** on Delhivery's real logistics delivery pipeline data. The goal is to clean, process and engineer meaningful features from raw delivery records to help the data science team build accurate forecasting models.

> **Delhivery** is India's largest and fastest-growing fully integrated logistics company by revenue. This analysis uncovers key operational patterns and inefficiencies across their delivery network.

---

## Problem Statement

The data engineering pipeline produces raw delivery data where:
- Each trip is split across **multiple rows** (one per route segment)
- Location fields contain **embedded geographic information** as unstructured strings
- Time fields are stored as **raw strings** instead of datetime objects
- Cumulative and segment metrics are **mixed** without clear separation

The objective is to transform this raw data into a clean, structured, model-ready dataset while extracting actionable business insights.

---

## Dataset

| Property | Value |
|---|---|
| Total Records | 144,867 rows |
| Columns | 24 |
| Time Period | 2018 (9 months) |
| Unique Trips (after aggregation) | 14,787 |
| Route Types | FTL, Carting |

---

## Project Structure

```
delhivery-feature-engineering/
│
├── Delhivery_Feature_Engineering.ipynb   # Main notebook
├── delhivery_data.csv                    # Dataset
└── README.md                             # Project documentation
```

---

## Steps Performed

### 1. Exploratory Data Analysis
- Shape, data types, missing value detection, statistical summary
- Distribution plots for all 8 numerical columns
- Boxplots by route type (FTL vs Carting)
- Scatter plots between continuous variable pairs
- Correlation heatmap

### 2. Data Cleaning
- Dropped 551 rows with null source/destination names (0.38%)
- Converted datetime columns from object to datetime64
- Converted categorical columns to category dtype

### 3. Feature Engineering
- Extracted `source_city`, `source_state` from source_name strings
- Extracted `destination_city`, `destination_state` from destination_name strings
- Extracted `trip_month`, `trip_year`, `trip_day`, `trip_weekday`, `trip_hour` from timestamps
- Created `od_time_diff` — trip duration in hours from od_start and od_end times
- Created `corridor` — source state to destination state pair
- Created `is_intra_state` — boolean flag for same-state deliveries

### 4. Row Aggregation
- Grouped 144,316 rows by `trip_uuid` into 14,787 unique trip records
- Applied strategy: sum for segments, last for end timestamps, first for categoricals

### 5. Hypothesis Testing (5 Paired T-Tests)

| Test | Columns Compared | T-Statistic | P-Value | Result |
|---|---|---|---|---|
| 1 | od_time_diff vs start_scan_to_end_scan | -33.88 | 0.0000 | Reject H0 |
| 2 | actual_time vs osrm_time | 32.43 | 0.0000 | Reject H0 |
| 3 | actual_time vs segment_actual_time | 30.73 | 0.0000 | Reject H0 |
| 4 | osrm_distance vs segment_osrm_distance | 30.01 | 0.0000 | Reject H0 |
| 5 | osrm_time vs segment_osrm_time | 30.27 | 0.0000 | Reject H0 |

### 6. Outlier Treatment
- Detected outliers using boxplots across all numerical columns
- Applied IQR capping (Winsorization) — values beyond 1.5×IQR fences are capped
- Most dramatic: actual_distance max dropped from **85,110 km → 1,077 km**

### 7. Categorical Encoding
- One-hot encoded `route_type` with `drop_first=True` to avoid dummy variable trap
- High cardinality columns (city, state) retained for business analysis only

### 8. Normalization
- Applied `StandardScaler` to all 9 numerical columns
- Mean = 0, Std = 1 verified for all columns post-scaling

---

## Key Business Findings

- **Actual delivery time is 2× OSRM estimated time** — route planning tools underestimate real delivery duration by nearly 100%
- **79.77% of trips are intra-state** — Delhivery operates primarily as a regional logistics provider
- **Trip creation peaks at 11 PM** — batch dispatch model processes orders at end of day
- **All top 10 inter-state corridors are in North India** — South India has zero inter-state representation despite highest intra-state volumes
- **Maharashtra and Karnataka dominate** — leading both source and destination rankings
- **Extreme data pipeline errors** — distances up to 85,110 km recorded (impossible within India)

---

## Top Recommendations

1. Recalibrate delivery time estimation tools using a 2× correction factor over OSRM
2. Build direct inter-state corridors connecting Maharashtra, Karnataka and Tamil Nadu
3. Reduce warehouse processing time — packages add 11+ hours beyond road transit
4. Implement real-time data validation to reject impossible distance values
5. Align workforce planning to Wednesday demand peaks and 11 PM dispatch windows

---

## Tech Stack

| Library | Purpose |
|---|---|
| Pandas | Data manipulation and aggregation |
| NumPy | Numerical operations |
| Matplotlib | Visualizations |
| Seaborn | Statistical plots |
| Scipy | Hypothesis testing (paired t-test) |
| Scikit-learn | StandardScaler normalization |

---

## How to Run

```bash
# Clone the repository
git clone https://github.com/yourusername/delhivery-feature-engineering.git

# Open in Google Colab or Jupyter
# Upload delhivery_data.csv to /content/
# Run all cells: Runtime -> Run All
```

---

## Limitations

- Dataset covers only 9 of 12 months of 2018 — full seasonal analysis not possible
- Four unknown columns (factor, segment_factor, is_cutoff, cutoff_factor) excluded
- IQR capping retains capped values at fence boundaries — true extremes not seen by model
- No pricing, demographic or competitor data available

---

## Author
Ayush Saini

[LinkedIn](https://linkedin.com/in/yourprofile) | [GitHub](https://github.com/yourusername)

---

## License

This project is for educational purposes as part of a data science case study.
