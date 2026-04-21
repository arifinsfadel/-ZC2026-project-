# -ZC2026-project-
DE ZoomCamp 2026


📊 🌍 CLIMATE DATA INTELLIGENCE PLATFORM
(CO₂ Emissions × Temperature Change Analytics)
🎯 PROBLEM DESCRIPTION / STATEMENT (PORTFOLIO VERSION)
🎯 Problem Statement

Global climate change is one of the most urgent challenges of our time, driven largely by rising greenhouse gas emissions from human activities. While CO₂ emissions and temperature data are widely available, they are often stored in separate datasets, making it difficult to analyze their relationship in a structured and scalable way.

This project addresses the challenge of:

Understanding how CO₂ emissions evolve across countries and time
Analyzing global and regional temperature changes
Measuring the relationship between emissions and temperature trends
Identifying countries with high climate impact intensity

By combining emissions data from Global Fossil CO2 Emissions Dataset and temperature data from global climate records, this project builds an end-to-end data pipeline that transforms raw environmental data into an analytical model and interactive dashboard.

The core idea of this project is to quantify climate impact using a derived metric:

🌡️ Climate Impact Index
Climate Impact Index = CO2 Growth Rate × Temperature Anomaly Change
High value → strong coupling between emissions and warming
Low value → weaker or delayed climate response

The final system enables users to:

Track long-term CO₂ emission trends per country
Analyze temperature changes across regions
Measure correlation between emissions and climate change
Compare climate impact across countries globally

This project demonstrates how modern data engineering can be applied to environmental data to generate actionable insights for climate awareness and policy analysis.

🏗️ ARCHITECTURE (CLEAN PORTFOLIO STYLE)
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
⚙️ STEP-BY-STEP IMPLEMENTATION (COPY-PASTE FRIENDLY)
🟢 STEP 1 — PROJECT STRUCTURE
climate-pipeline/
│
├── ingestion/
├── data/
│   ├── co2/
│   └── temperature/
│
├── airflow/
├── dbt/
├── terraform/
├── dashboard/
├── docker-compose.yml
└── README.md
🟢 STEP 2 — DATA INGESTION (PYTHON)
import pandas as pd

# Load datasets
co2 = pd.read_csv("data/co2/emissions.csv")
temp = pd.read_csv("data/temperature/temperature.csv")

# Standardize columns
co2.columns = co2.columns.str.lower()
temp.columns = temp.columns.str.lower()

# Clean missing values
co2 = co2.dropna()
temp = temp.dropna()

# Save as parquet (data lake format)
co2.to_parquet("data_lake/co2.parquet")
temp.to_parquet("data_lake/temp.parquet")
🟢 STEP 3 — UPLOAD KE DATA LAKE (GCS)
from google.cloud import storage

client = storage.Client()
bucket = client.bucket("climate-data-lake")

def upload(file_path, destination):
    blob = bucket.blob(destination)
    blob.upload_from_filename(file_path)

upload("data_lake/co2.parquet", "co2.parquet")
upload("data_lake/temp.parquet", "temp.parquet")
🟢 STEP 4 — LOAD KE BIGQUERY
from google.cloud import bigquery

client = bigquery.Client()

table_id = "climate.raw_co2"

job = client.load_table_from_uri(
    "gs://climate-data-lake/co2.parquet",
    table_id
)

job.result()
🟢 STEP 5 — DBT TRANSFORMATION
🔹 staging_co2.sql
SELECT
    country,
    year,
    co2_emissions
FROM {{ source('raw', 'co2') }}
🔹 staging_temperature.sql
SELECT
    country,
    year,
    temperature_anomaly
FROM {{ source('raw', 'temperature') }}
🔥 CORE MODEL (JOIN)
fct_climate_analysis.sql
SELECT
    c.country,
    c.year,
    c.co2_emissions,
    t.temperature_anomaly,

    (c.co2_emissions - LAG(c.co2_emissions) OVER (PARTITION BY c.country ORDER BY c.year))
    / NULLIF(LAG(c.co2_emissions) OVER (PARTITION BY c.country ORDER BY c.year), 0)
    AS co2_growth_rate,

    (t.temperature_anomaly - LAG(t.temperature_anomaly) OVER (PARTITION BY t.country ORDER BY t.year))
    AS temp_change,

    (
      (c.co2_emissions - LAG(c.co2_emissions) OVER (PARTITION BY c.country ORDER BY c.year))
      *
      (t.temperature_anomaly - LAG(t.temperature_anomaly) OVER (PARTITION BY t.country ORDER BY t.year))
    ) AS climate_impact_index

FROM {{ ref('staging_co2') }} c
JOIN {{ ref('staging_temperature') }} t
ON c.country = t.country AND c.year = t.year
📊 STEP 6 — DASHBOARD DESIGN
PAGE 1 — GLOBAL CLIMATE TRENDS
CO2 emissions over time (line chart)
Global temperature anomaly (line chart)
PAGE 2 — CLIMATE IMPACT ANALYSIS
Scatter plot: CO2 vs Temperature
Climate Impact Index by country
Top 10 most affected countries
🧱 FINAL REPO TEMPLATE (PORTFOLIO READY)
climate-data-platform/
│
├── README.md
├── ingestion/
│   └── ingest.py
│
├── dbt/
│   ├── models/
│   │   ├── staging_co2.sql
│   │   ├── staging_temp.sql
│   │   └── fct_climate_analysis.sql
│
├── terraform/
├── airflow/
├── dashboard/
│   └── looker_link.txt
│
├── data/
│   ├── raw/
│   └── processed/
│
└── docker-compose.yml
