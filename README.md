# -ZC2026-project-
DE ZoomCamp 2026


## 🌍 Climate Data Intelligence Platform  
## CO₂ Emissions × Temperature Analytics (2002–2022)

---

## 🚀 Overview

Climate change is one of the most critical global challenges of our time. While CO₂ emissions and temperature data are widely available, they are often fragmented and lack integrated analysis.

This project builds an end-to-end data engineering pipeline that combines:

- CO₂ emissions data (country-level fossil fuel emissions)
- Global temperature anomaly data

to analyze the relationship between human activity and climate change.

---

## 🎯 Problem Statement

Understanding the relationship between CO₂ emissions and temperature change is difficult due to disconnected datasets and lack of structured analytical pipelines.

This project addresses:

- How CO₂ emissions evolve across countries and time  
- How global temperature anomalies change over time  
- Whether emissions and temperature are statistically correlated  
- How to detect abnormal climate events (e.g. COVID period, heatwaves)  

---

## 📊 Key Analytical Features (UPGRADE VALUE)

### 🌡️ 1. Climate Impact Index
Climate Impact Index = CO₂ Growth Rate × Temperature Change


Measures how strongly emissions changes are associated with temperature changes.

---

### 📈 2. Correlation Analysis (Pearson r)

We compute statistical correlation between:

- CO₂ emissions
- Temperature anomalies

```text id="corr"

Pearson r → measures strength of linear relationship


Example insights:

r > 0.7 → strong climate coupling
r < 0.3 → weak relationship or lag effect
🚨 3. Anomaly Detection

We identify unusual climate patterns such as:

COVID-19 emission drop (2020)
Extreme heatwave years
Sudden emission spikes

Method:

Z-score normalization
Year-over-year deviation analysis

---

## 🧱 Architecture


                ┌──────────────────────────────┐
                │   DATA SOURCES               │
                │                              │
                │  CO2 Emissions Dataset       │
                │  Temperature Dataset         │
                │  (Kaggle CSV files)          │
                └─────────────┬────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │        INGESTION LAYER              │
        │  Python (Pandas + PyArrow)         │
        │  Data validation & cleaning        │
        └─────────────┬──────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │          DATA LAKE                  │
        │   Google Cloud Storage (GCS)       │
        │   Raw Parquet Files                │
        └─────────────┬──────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │      DATA WAREHOUSE                │
        │      BigQuery                      │
        │  (Partitioned by year & country)  │
        └─────────────┬──────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │      TRANSFORMATION LAYER         │
        │            dbt                    │
        │  - Staging models                │
        │  - Fact tables                  │
        │  - Climate metrics             │
        └─────────────┬──────────────────────┘
                              │
                              ▼
        ┌──────────────────────────────────────┐
        │         DASHBOARD                 │
        │   Looker Studio / Streamlit      │
        │  - Trend analysis               │
        │  - Correlation view            │
        │  - Country comparison         │
        └──────────────────────────────────────┘
Kaggle Datasets
      ↓
Python Ingestion (Pandas)
      ↓
Google Cloud Storage (Data Lake)
      ↓
BigQuery (Data Warehouse)
      ↓
dbt Transformations (Staging + Fact Models)
      ↓
Analytics Layer (Climate Metrics)
      ↓
Looker Studio Dashboard
``` id="arch_v2"

---

## ⚙️ Tech Stack

| Layer            | Tools |
|------------------|------|
| Ingestion        | Python (Pandas, PyArrow) |
| Data Lake        | Google Cloud Storage |
| Data Warehouse   | BigQuery |
| Transformation   | dbt |
| Orchestration    | Airflow (optional) |
| Visualization    | Looker Studio |
| Cloud            | Google Cloud Platform |

---

## 📊 Data Model

### Fact Table: `fct_climate_analysis`

| Column | Description |
|--------|-------------|
| country | Country name |
| year | Year (2002–2022) |
| co2_emissions | Total CO₂ emissions |
| temperature_anomaly | Temperature deviation |
| co2_growth_rate | YoY emission growth |
| temp_change | YoY temperature change |
| climate_impact_index | Combined impact metric |
| anomaly_flag | Detected abnormal events |

---

## 📈 Dashboard (Looker Studio)

### Page 1 — Global Climate Overview
- CO₂ emissions trend (2002–2022)
- Global temperature anomaly trend
- Emission distribution by country

---

### Page 2 — Climate Relationship Analysis

- 🌡️ CO₂ vs Temperature scatter plot  
- 📊 Climate Impact Index trend over time  
- 📈 Pearson correlation visualization  

---

### Page 3 — Climate Anomalies

- 🚨 COVID-19 emission drop (2020 highlight)
- 🌡️ Heatwave anomaly years
- 📉 Sudden emission shocks per country

---

## 🧠 Key Insights

This project enables discovery of:

- Long-term relationship between emissions and temperature
- Countries with highest climate impact contribution
- Structural changes during global events (COVID-19)
- Hidden anomalies in climate behavior

---

## 🔄 Pipeline Flow

1. Extract datasets from Kaggle
2. Clean & normalize data (Python)
3. Store raw data in GCS (Data Lake)
4. Load into BigQuery (Warehouse)
5. Transform using dbt:
   - staging models
   - fact tables
   - analytical metrics
6. Visualize in Looker Studio

---

## 🚀 How to Run

```bash
git clone https://github.com/arifinsfadel/-ZC2026-project-/
cd climate-platform

pip install -r requirements.txt

python ingestion/ingest.py

dbt run

# open Looker Studio dashboard link

##📚 Data Sources & References
##🌍 Primary Datasets
| Dataset                     | Source         | Indicator Code       | Description                                                                          |
| --------------------------- | -------------- | -------------------- | ------------------------------------------------------------------------------------ |
| CO₂ Emissions (Fossil Fuel) | Kaggle / EDGAR | EN.GHG.CO2.MT.CE.AR5 | Fossil fuel CO₂ emissions excluding LULUCF, based on EDGAR v8.0 methodology          |
| Temperature Anomalies       | Kaggle         | —                    | Global temperature deviation from long-term baseline, used to analyze climate trends |

https://www.kaggle.com/datasets/thedevastator/global-fossil-co2-emissions-by-country-2002-2022
https://www.kaggle.com/datasets/adilshamim8/temperature

##📌 Project Impact

This project demonstrates a junior data engineer–level system that includes:

End-to-end data pipeline
Cloud data architecture
Analytical modeling
Statistical analysis (correlation + anomaly detection)
Business intelligence dashboard
##👤 Author

Built as a data engineering portfolio project focused on climate analytics and real-world data pipeline design.


