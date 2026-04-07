# 🏦 Sentinel: Enterprise Banking Data Ecosystem
### *Real-time CDC Pipeline, Medallion Architecture & Automated Governance*

**Sentinel** is a production-grade data engineering platform designed to solve the "stale data" problem in retail banking. By implementing **Log-based Change Data Capture (CDC)**, it synchronizes transactional OLTP systems with an analytical Snowflake Warehouse in near real-time, ensuring that financial reports are never a day late.

---

## 🏗️ The Architecture
Sentinel transforms raw transactional events into high-fidelity business intelligence using the **Medallion (Bronze/Silver/Gold) Architecture**.

1.  **The Pulse (Source):** A custom Python-Faker engine simulates high-concurrency banking traffic (Accounts, Customers, Transactions) into **PostgreSQL**.
2.  **The Veins (Streaming):** **Debezium** monitors the Postgres WAL (Write-Ahead Logs), streaming every row-level change into **Kafka** topics.
3.  **The Buffer (Landing):** A specialized consumer persists Kafka streams into **MinIO (S3-compatible)** to ensure zero data loss and replayability.
4.  **The Brain (Warehouse):** **Snowflake** ingests the raw files. **dbt** then transforms JSON/Avro blobs into structured relational models.
5.  **The Heart (Orchestration):** **Apache Airflow** manages the lineage, ensuring snapshots and marts run only after successful ingestion.

---

## ⚡ Technical Excellence

| Layer | Technology | Key Implementation |
| :--- | :--- | :--- |
| **Source** | `PostgreSQL` | ACID-compliant ledger acting as the Core Banking System. |
| **CDC / Streaming** | `Debezium` + `Kafka` | Log-based capture to avoid performance hits on production DBs. |
| **Storage / Lake** | `MinIO (S3)` | Persistent landing zone for "Raw" unstructured data. |
| **Warehouse** | `Snowflake` | Multi-cluster compute for heavy ELT workloads. |
| **Modeling** | `dbt Core` | SCD Type-2 snapshots for historical balance auditing. |
| **Orchestration** | `Apache Airflow` | Sensor-based DAGs triggering on file arrival in MinIO. |
| **DevOps** | `GitHub Actions` | Automated `dbt-test` and `sqlfluff` linting on every PR. |

---

## 💎 Advanced Features

### 🕒 Temporal Data Tracking (SCD Type 2)
In banking, current state is only half the story. Sentinel utilizes **dbt Snapshots** to maintain a full historical audit trail of customer profiles and account statuses, enabling **Point-in-Time** reporting and regulatory compliance.

### 🛡️ Data Governance & Integrity
* **Schema Enforcement:** Debezium handles upstream schema evolutions without breaking downstream pipelines.
* **Automated Testing:** Every `dbt run` is protected by `unique`, `not_null`, and `relationship` tests.
* **CI/CD Guardrails:** The pipeline automatically blocks merges if data quality tests fail or if SQL doesn't meet enterprise style guides.

---

## 📂 Repository Blueprint

```text
banking-modern-datastack/
├── .github/workflows/      # DataOps: CI/CD (Linter, Test, Deploy)
├── sentinel_dbt/           # The Transformation Logic
│   ├── models/             
│   │   ├── bronze/         # Raw Ingestion (ODS)
│   │   ├── silver/         # Cleaned, de-duplicated Normalized models
│   │   └── gold/           # Analytics-ready Marts (Facts/Dims)
│   ├── snapshots/          # SCD2 History Tracking logic
├── infrastructure/         
│   ├── airflow/            # DAGs & Orchestration sensors
│   ├── kafka-connect/      # Debezium & S3 Sink configurations
│   └── data-generator/     # High-concurrency transaction simulator
├── docker-compose.yml      # Entire environment in a single command
└── README.md
