# 🔍 Real-Time Fraud Detection System
### Built on Databricks Community Edition | Delta Lake | Spark ML | MLflow

![Databricks](https://img.shields.io/badge/Databricks-Community%20Edition-FF3621?style=for-the-badge&logo=databricks)
![Apache Spark](https://img.shields.io/badge/Apache%20Spark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta%20Lake-003366?style=for-the-badge)
![MLflow](https://img.shields.io/badge/MLflow-0194E2?style=for-the-badge&logo=mlflow)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)

---

## 📌 Project Overview

A production-grade **end-to-end fraud detection pipeline** built entirely on **Databricks Community Edition (Free Tier)**. This project demonstrates the full lifecycle of a real-world data engineering and ML system — from raw data ingestion to live transaction scoring.

> **Every transaction that enters this system gets a verdict: ✅ APPROVE or 🚨 BLOCK**

---

## 🏗️ Architecture

```
💳 Raw Transactions (Simulated Bank Feed)
          │
          ▼
   ┌─────────────┐
   │  🪨 BRONZE  │  ← Raw storage. Append-only. Never modified.
   │  Delta Lake │    Time Travel enabled. 1,500 transactions.
   └──────┬──────┘
          │
          ▼
   ┌─────────────┐
   │  🥈 SILVER  │  ← Cleaned + Feature Engineered.
   │  Delta Lake │    6 fraud signals. Risk scoring. 
   └──────┬──────┘
          │
          ▼
   ┌─────────────┐
   │  🥇 GOLD    │  ← 5 Business Aggregation Tables.
   │  Delta Lake │    Country, Merchant, Hour, Customer, KPI.
   └──────┬──────┘
          │
          ▼
   ┌─────────────┐
   │ 🤖 ML MODEL │  ← Random Forest (50 trees).
   │   MLflow    │    Tracked, versioned, logged.
   └──────┬──────┘
          │
          ▼
   ┌─────────────┐
   │ ⚡ SCORING  │  ← Live transaction scoring.
   │   Engine    │    BLOCK / APPROVE + probability.
   └─────────────┘
```

---

## 📁 Project Structure

```
Project_2_Fraud_Detection/
│
├── 00_Setup_Config.py        # Project config, paths, schema contract
├── 01_Bronze_Layer.py        # Raw ingestion → Delta Lake
├── 02_Silver_Layer.py        # Cleaning + Feature Engineering
├── 03_Gold_Layer.py          # Business aggregation tables
├── 04_ML_FraudModel.py       # ML Pipeline + MLflow tracking
├── 05_Live_Scoring.py        # Real-time transaction scoring
└── README.md                 # You are here
```

---

## 🗄️ Delta Tables Created

| Table | Layer | Rows | Purpose |
|-------|-------|------|---------|
| `fraud_db.bronze_transactions` | 🪨 Bronze | 1,500 | Raw transaction archive |
| `fraud_db.silver_transactions` | 🥈 Silver | 1,500 | Cleaned + featured data |
| `fraud_db.gold_fraud_by_country` | 🥇 Gold | 9 | Fraud rate by country |
| `fraud_db.gold_fraud_by_merchant` | 🥇 Gold | 6 | Fraud rate by merchant |
| `fraud_db.gold_fraud_by_hour` | 🥇 Gold | 17 | Fraud rate by hour |
| `fraud_db.gold_risky_customers` | 🥇 Gold | 74 | High-risk customer list |
| `fraud_db.gold_kpi_summary` | 🥇 Gold | 1 | Executive KPI dashboard |
| `fraud_db.model_predictions` | 🤖 ML | 290 | Test set predictions |
| `fraud_db.live_scored_transactions` | ⚡ Scoring | 9 | Live scored decisions |

---

## 🧠 Features Engineered

| Feature | Logic | Fraud Signal |
|---------|-------|-------------|
| `is_night_transaction` | hour between 0–5 | Fraud peaks at 3AM |
| `is_high_amount` | amount > $1,000 | Fraudsters go big |
| `is_suspicious_country` | NG, RO, PK, XX | High-risk geographies |
| `is_multiple_attempts` | login_attempts > 2 | Account takeover signal |
| `is_online_or_atm` | channel = online/atm | Riskiest channels |
| `fraud_risk_score` | sum of all above (0–5) | Composite risk score |

---

## 🤖 ML Model Details

| Property | Value |
|----------|-------|
| Algorithm | Random Forest Classifier |
| Trees | 50 |
| Max Depth | 6 |
| Train Split | 80% |
| Test Split | 20% |
| Tracking | MLflow Experiment |
| Features | 9 engineered columns |

---

## 🚀 How to Run This Project

### Prerequisites
- Databricks Community Edition account (free) → [Sign up here](https://community.cloud.databricks.com)
- Single-node cluster (any runtime with Delta Lake support)

---

### Step 1 — Clone This Repo

```bash
git clone https://github.com/YOUR_USERNAME/fraud-detection-databricks.git
```

---

### Step 2 — Set Up Databricks Workspace

1. Log in to [community.cloud.databricks.com](https://community.cloud.databricks.com)
2. Go to **Workspace → Users → your email**
3. Create a folder called `Project_2_Fraud_Detection`
4. Import each `.py` notebook file into this folder

**To import a notebook:**
- Click the folder → **Create → Import**
- Select the `.py` file from your cloned repo
- Databricks auto-converts it to a notebook

---

### Step 3 — Start Your Cluster

1. Go to **Compute** in the left sidebar
2. Click **Create Compute**
3. Use default settings (single node)
4. Click **Create** and wait for it to start (2–3 mins)

---

### Step 4 — Run Notebooks In Order

> ⚠️ **Always run in this exact order.** Each notebook depends on the previous one.

| Order | Notebook | What It Does | Expected Output |
|-------|----------|-------------|----------------|
| 1️⃣ | `00_Setup_Config` | Creates database + schema | 🟢 ALL SYSTEMS GO |
| 2️⃣ | `01_Bronze_Layer` | Ingests 1,500 transactions | 1,500 rows in Bronze |
| 3️⃣ | `02_Silver_Layer` | Cleans + adds fraud features | 1,500 rows in Silver |
| 4️⃣ | `03_Gold_Layer` | Builds 5 business tables | 🟢 Medallion COMPLETE |
| 5️⃣ | `04_ML_FraudModel` | Trains + tracks ML model | AUC-ROC printed |
| 6️⃣ | `05_Live_Scoring` | Scores new transactions | BLOCK/APPROVE decisions |

---

### Step 5 — Verify Everything Worked

After running all notebooks, run this in any notebook cell:

```python
spark.sql("USE fraud_db")
spark.sql("SHOW TABLES").show(20, False)
```

You should see **9 tables** listed.

---

## ⚠️ Known Community Edition Limitations & Fixes

This project was built specifically to work around Databricks Community Edition restrictions:

| Limitation | Error | Our Fix |
|-----------|-------|---------|
| DBFS disabled | `DBFS_DISABLED` | Use managed tables via `saveAsTable()` |
| Unity Catalog missing | `NO_SUCH_CATALOG_EXCEPTION` | Use `os.makedirs()` instead of `dbutils.fs` |
| Local FS forbidden | `SecurityException` | Use `spark.createDataFrame()` directly |
| MLflow artifact store needs UC | `MlflowException` | Save predictions to Delta table instead |
| No cross-notebook variables | `NameError: model` | Retrain model at start of scoring notebook |

---

## 📊 Key Concepts Demonstrated

```
✅ Medallion Architecture      (Bronze → Silver → Gold)
✅ Delta Lake                  (ACID, versioning, time travel)
✅ Schema Enforcement          (schema-on-write)
✅ Data Quality Checks         (null, duplicate, range validation)
✅ Feature Engineering         (6 domain-driven fraud signals)
✅ Heuristic Risk Scoring      (rule-based composite score)
✅ Spark ML Pipeline           (Assemble → Scale → RandomForest)
✅ MLflow Experiment Tracking  (params, metrics, tags, run ID)
✅ Live Transaction Scoring    (model.transform on new data)
✅ Audit Trail                 (every decision persisted in Delta)
```

---

## 📈 Sample Output — Live Scoring

```
🏦 FRAUD DETECTION DECISION ENGINE
══════════════════════════════════════════════════════════
  TXN ID               Amount   Country  Hr  Risk   Prob    Decision
──────────────────────────────────────────────────────────
  NEW_TXN_9234       $5821.00   NG        2     5  0.9821  🚨 BLOCK   ✅
  NEW_TXN_9156         $45.20   US       14     0  0.0234  ✅ APPROVE ✅
  NEW_TXN_9445       $1050.00   IN       23     2  0.4821  ✅ APPROVE ✅
  NEW_TXN_9312       $3200.00   XX        3     5  0.9654  🚨 BLOCK   ✅
══════════════════════════════════════════════════════════
```

---

## 🗺️ What's Next (Week 3 Roadmap)

- [ ] Convert notebooks into **Databricks Jobs** with task dependencies
- [ ] Add **email alerts** on pipeline failures
- [ ] Implement **Delta Live Tables** for true streaming ingestion
- [ ] Add **MERGE** logic to handle late-arriving duplicate transactions
- [ ] Build a **Databricks SQL Dashboard** on top of Gold tables

---

## 👤 Author

**Zeeshan Ahmed**
- Built during Week 2 of structured Databricks learning
- Platform: Databricks Community Edition (Free Tier)
- Week 1 Project: [End-to-End Sales Analytics Pipeline](https://github.com/YOUR_USERNAME/databricks-sales-pipeline)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

> 💡 **Tip for Recruiters:** Every error encountered during this project (DBFS disabled, Unity Catalog missing, Spark ML Vector extraction, MLflow artifact restrictions) was debugged and solved — demonstrating real production troubleshooting skills, not just tutorial-following.
