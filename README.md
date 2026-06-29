# BranchLens — Branch Performance & Profitability Intelligence System

An end-to-end Data Engineering pipeline built using Python, PySpark, Databricks, and Snowflake, implementing the Medallion Architecture (Bronze → Silver → Gold) to analyze branch-level banking performance across 50 branches over 3 years.

---

## Architecture

```
Raw CSV Data
     ↓
[BRONZE LAYER] — Raw data ingested into Databricks
     ↓
[SILVER LAYER] — Cleaned, validated, enriched data
     ↓
[GOLD LAYER]   — Aggregated business insights
     ↓
[SNOWFLAKE]    — Cloud data warehouse for reporting
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Data generation, pipeline logic |
| PySpark | Distributed data processing |
| Databricks | Cloud workspace, pipeline execution |
| Snowflake | Cloud data warehouse |
| SQL | Business queries and reporting |

---

## Dataset

Synthetic banking dataset generated using Python covering:
- 50 branches across India (North, South, East, West regions)
- 36 months of data (2021–2023)
- 1,800 rows and 25 columns
- Metrics: Revenue, Profit, Expenses, Loans, Deposits, NPA Ratio, CSAT, Digital Transactions

4 anomalies injected for realistic analysis:
- Kankurgachi — CSAT crash in Q3 2022
- Lower Parel — Expense spike in Nov 2023
- Kanpur Main — Persistently high NPA ratio
- Hebbal — Rapid digital adoption spike in 2023

---

## Pipeline Details

### Bronze Layer
- Raw CSV ingested into Databricks
- Schema validation using inferSchema
- Null checks and duplicate detection
- 1,800 rows | 25 columns

### Silver Layer
- Added business logic columns:
  - `profit_status` — Profitable / Loss
  - `performance_tier` — Excellent / Good / Average / Poor
  - `npa_health` — Healthy / Moderate / Stressed
  - `branch_avg_csat` — Branch average CSAT using Window Functions
  - `csat_anomaly` — Flags months where CSAT dropped 20% below branch average
- 1,800 rows | 30 columns

### Gold Layer
Three business-ready tables:

| Table | Description | Rows |
|-------|-------------|------|
| GOLD_BRANCH_PERFORMANCE | Revenue, profit, CSAT per branch | 50 |
| GOLD_REGION_SUMMARY | Performance aggregated by region | 4 |
| GOLD_ANOMALY_DETECTION | All flagged anomaly records | 433 |

---

## Snowflake Tables

All Gold layer tables loaded into Snowflake under `BRANCH_LENS_DB.PUBLIC`:

```sql
-- Top 5 branches by revenue
SELECT BRANCH_NAME, TOTAL_REVENUE
FROM GOLD_BRANCH_PERFORMANCE
ORDER BY TOTAL_REVENUE DESC
LIMIT 5;

-- Region with highest NPA ratio
SELECT REGION, AVERAGE_NPA_RATIO
FROM GOLD_REGION_SUMMARY
WHERE AVERAGE_NPA_RATIO = (
    SELECT MAX(AVERAGE_NPA_RATIO)
    FROM GOLD_REGION_SUMMARY
);

-- Top 5 anomaly prone branches
SELECT BRANCH_NAME, CITY, COUNT(*) AS ANOMALY_COUNT
FROM GOLD_ANOMALY_DETECTION
GROUP BY BRANCH_ID, BRANCH_NAME, CITY
ORDER BY COUNT(*) DESC
LIMIT 5;
```

---

## Project Structure

```
branchlens/
├── BranchLens_Project.ipynb     # Main pipeline notebook
├── generate_branch_data.ipynb   # Synthetic data generation
├── branch_data.csv              # Generated dataset
└── README.md
```

---

## Key Concepts Demonstrated

- Medallion Architecture (Bronze → Silver → Gold)
- PySpark Window Functions for anomaly detection
- Databricks to Snowflake integration
- ETL pipeline design and execution
- Business intelligence through SQL queries

---

## Author

**D. Anita Patro**
Data Engineer | Python | SQL | Snowflake | Databricks
[GitHub](https://github.com/danitapatro) | [LinkedIn](https://www.linkedin.com/in/d-anita-patro)
