# 🚦 Urban Transportation Pipeline — Databricks Lakeflow (Spark Declarative Pipelines)

[![Databricks](https://img.shields.io/badge/Databricks-Lakeflow-FF3621?logo=databricks)](https://www.databricks.com/)
[![Delta Lake](https://img.shields.io/badge/Delta-Lake-003366?logo=apachespark)](https://delta.io/)
[![PySpark](https://img.shields.io/badge/PySpark-3.x-E25A1C?logo=apachespark)](https://spark.apache.org/)
[![Python](https://img.shields.io/badge/Python-3.9+-3776AB?logo=python)](https://www.python.org/)

An **end-to-end data engineering project** in the urban transportation domain, built with **Databricks Lakeflow (Spark Declarative Pipelines)**, Delta Lake, and the Medallion Architecture (Bronze → Silver → Gold).

---

## 📐 Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        DATA SOURCES                             │
│   GPS Trackers │ Dispatch Systems │ IoT Sensors │ Fleet DB      │
└──────────────────────────┬──────────────────────────────────────┘
                           │  (Auto Loader / cloudFiles)
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                     🥉 BRONZE LAYER                             │
│   bronze_trips  │ bronze_vehicles │ bronze_routes │ bronze_events│
│   • Raw ingestion, no transformations                           │
│   • Adds _ingested_at, _source_file metadata                    │
│   • Incremental via Auto Loader                                 │
└──────────────────────────┬──────────────────────────────────────┘
                           │  (@dlt.expect / @dlt.expect_or_drop)
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                     🥈 SILVER LAYER                             │
│  silver_trips │ silver_vehicles │ silver_routes │ silver_events  │
│   • Schema enforcement & type casting                           │
│   • Data quality expectations                                   │
│   • Deduplication, null handling                                │
│   • Enrichment via joins                                        │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                     🥇 GOLD LAYER                               │
│  gold_daily_trip_summary  │ gold_route_performance              │
│  gold_vehicle_utilization │ gold_incident_impact                │
│   • Business KPIs & aggregations                                │
│   • BI / Dashboard ready                                        │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📊 Dataset Overview

| Dataset      | Rows   | Description                                      |
|-------------|--------|--------------------------------------------------|
| Trips        | 5,000  | Ride records (fare, duration, delay, status)     |
| Vehicles     | 200    | Fleet metadata (type, capacity, fuel type, city) |
| Routes       | 50     | Route definitions with zones and distances       |
| Events       | 500    | Incidents (breakdown, delay, accident, etc.)     |

---

## 🔑 Key Lakeflow Concepts

| Concept | Usage in this Project |
|---------|----------------------|
| `@dlt.table` | Declarative Bronze, Silver, Gold table definitions |
| `@dlt.expect` | Soft data quality checks (logged, not dropped) |
| `@dlt.expect_or_drop` | Hard DQ checks — failing rows are dropped |
| `cloudFiles` (Auto Loader) | Incremental, scalable ingestion from landing zone |
| `dlt.read()` | Dependency-aware table references (builds DAG) |
| Delta Lake | ACID transactions, CDF, time travel |
| Unity Catalog | Governed, discoverable data assets |

---

## 📁 Project Structure

```
lakeflow-transport-pipeline/
├── notebooks/
│   └── lakeflow_transport_pipeline.ipynb   # Main pipeline notebook
├── configs/
│   └── pipeline_config.json                # Lakeflow pipeline config
├── data/
│   └── sample_schema.json                  # Sample schemas for reference
├── docs/
│   └── architecture.md                     # Detailed architecture notes
├── .github/
│   └── workflows/
│       └── ci.yml                          # CI workflow (lint + validate)
├── requirements.txt
├── .gitignore
└── README.md
```

---

## 🚀 Getting Started

### Option A: Run on Databricks (Recommended)

1. **Clone this repo** into your Databricks workspace:
   ```
   Databricks Repos → Add Repo → paste this GitHub URL
   ```

2. **Create a Lakeflow pipeline** in Databricks:
   - Go to **Workflows → Delta Live Tables → Create Pipeline**
   - Set source to `notebooks/lakeflow_transport_pipeline.ipynb`
   - Use the config from `configs/pipeline_config.json`

3. **Click Start** — Databricks handles the rest!

### Option B: Run Locally (PySpark simulation)

```bash
# 1. Clone the repo
git clone https://github.com/<your-username>/lakeflow-transport-pipeline.git
cd lakeflow-transport-pipeline

# 2. Create a virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Launch Jupyter
jupyter notebook notebooks/lakeflow_transport_pipeline.ipynb
```

> **Note:** Local runs use PySpark + Delta Lake to simulate the Lakeflow pipeline. The `@dlt.*` cells are printed as reference — execute the PySpark simulation cells to see full output.

---

## 📈 Business KPIs Produced

| Gold Table | Metrics |
|------------|---------|
| `gold_daily_trip_summary` | Revenue, trips, passengers, delay rate by city & vehicle type |
| `gold_route_performance` | Revenue/km, efficiency ratio, avg delay per route |
| `gold_vehicle_utilization` | Reliability score, passengers carried, routes served |
| `gold_incident_impact` | Incident counts, vehicles/routes affected by severity |

---

## 🛠️ Tech Stack

- **Databricks** — Lakeflow (Spark Declarative Pipelines)
- **Apache Spark** — PySpark 3.x
- **Delta Lake** — ACID storage layer
- **Auto Loader** — Incremental file ingestion
- **Unity Catalog** — Data governance
- **Python 3.9+**

---

## 🧠 What I Learned

- Designing declarative pipelines using the `@dlt.table` pattern
- Implementing **data quality expectations** at the Silver layer
- Building a **Medallion Architecture** with clear separation of concerns
- Using **Auto Loader** (`cloudFiles`) for scalable, incremental ingestion
- Constructing **business-ready Gold tables** for BI consumption
- Understanding pipeline DAG dependencies via `dlt.read()`

---


