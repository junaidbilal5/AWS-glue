# AWS Glue Data Engineering Project

This repository demonstrates end-to-end data engineering workflows using AWS Glue and related AWS services. It covers core ETL concepts, data cataloging, transformation, and orchestration with production-oriented patterns.

---

## 📌 Project Overview

The goal of this project is to build scalable, serverless ETL pipelines using AWS-native tools, focusing on:
- Reliable data ingestion into a data lake
- Automated schema discovery & cataloging
- Distributed transformations with PySpark
- Incremental processing (CDC-like patterns)
- Workflow orchestration and scheduling

---

## 🏗️ Architecture Diagram

```
           +-----------------------+
           |   Source Systems      |
           |-----------------------|
           | RDS / APIs / Files    |
           +-----------+-----------+
                       |
                       v
           +-----------------------+
           |     Amazon S3 (Raw)   |
           |   /bronze (landing)   |
           +-----------+-----------+
                       |
                       v
           +-----------------------+
           |  AWS Glue Crawler     |
           |  (Schema Discovery)   |
           +-----------+-----------+
                       |
                       v
           +-----------------------+
           | Glue Data Catalog     |
           | (Tables & Metadata)   |
           +-----------+-----------+
                       |
                       v
           +-----------------------+
           | AWS Glue ETL Jobs     |
           | (PySpark / Visual)    |
           +-----------+-----------+
                       |
         +-------------+-------------+
         |                           |
         v                           v
+-------------------+     +-------------------+
| S3 (Silver Layer) |     | S3 (Gold Layer)   |
| Cleaned Data      |     | Aggregated Data   |
+---------+---------+     +---------+---------+
          |                           |
          v                           v
   +--------------+           +--------------+
   | Athena / SQL |           | BI Tools     |
   | Query Layer  |           | (Power BI)   |
   +--------------+           +--------------+

Orchestration:
- AWS Lambda → Trigger jobs
- Glue Workflows → Manage dependencies
- Scheduler (cron) → Automated runs
```

---

## 🛠️ Tech Stack

- AWS Glue (ETL & Data Catalog)
- Amazon S3 (Data Lake Storage)
- AWS Lambda (Event-driven triggers)
- PySpark (Distributed processing)
- SQL / Athena (Querying)

---

## 📚 Detailed Concepts & Definitions

### 1. ETL vs ELT
- **ETL (Extract → Transform → Load):** Data is transformed before loading into the target system.
- **ELT (Extract → Load → Transform):** Raw data is first loaded, then transformed inside the data platform (used in this project).

---

### 2. AWS Glue
**Definition:** Serverless data integration service that makes it easy to discover, prepare, and combine data.

**Key Components:**
- Glue Jobs (ETL logic)
- Glue Data Catalog (metadata)
- Crawlers (schema inference)
- Workflows (orchestration)

---

### 3. Amazon S3 (Data Lake Layers)
- **Bronze:** Raw data (immutable)
- **Silver:** Cleaned & validated data
- **Gold:** Aggregated & business-ready data

---

### 4. Glue Data Catalog
**Definition:** Central metadata repository storing table schemas and partitions.

---

### 5. Glue Crawlers
**Definition:** Automatically scans data in S3 and creates/updates tables.

**Key Feature:**
- Schema evolution (detect new columns automatically)

---

### 6. Data Partitioning
**Definition:** Splitting data into folders (e.g., by date) to improve performance.

Example:
```
s3://bucket/sales/year=2025/month=03/
```

---

### 7. Incremental Data Loading
**Definition:** Processing only new or updated data instead of full reload.

Techniques:
- Timestamp-based filtering
- Partition-based ingestion

---

### 8. Orchestration
- **Lambda:** Trigger Glue jobs
- **Glue Workflows:** Manage dependencies
- **Scheduler:** Automate pipelines

---

## 💻 Code Examples

### 1. PySpark ETL Script (Glue Job)

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col

spark = SparkSession.builder.appName("GlueETL").getOrCreate()

# Read from S3 (Bronze)
df = spark.read.csv("s3://your-bucket/raw/data.csv", header=True, inferSchema=True)

# Transformations
clean_df = df.filter(col("price") > 0)

# Write to S3 (Silver)
clean_df.write.mode("overwrite").parquet("s3://your-bucket/silver/data/")
```

---

### 2. Incremental Load Example

```python
from pyspark.sql.functions import col

last_processed_date = "2025-01-01"

incremental_df = df.filter(col("updated_at") > last_processed_date)
```

---

### 3. SQL (CTAS Example)

```sql
CREATE TABLE sales_summary
WITH (
  format = 'PARQUET'
) AS
SELECT product_id, SUM(amount) AS total_sales
FROM sales
GROUP BY product_id;
```

---

### 4. Lambda Trigger (Python)

```python
import boto3

glue = boto3.client('glue')

def lambda_handler(event, context):
    response = glue.start_job_run(
        JobName='my-glue-job'
    )
    return response
```

---

## 🚀 Getting Started

### Prerequisites
- AWS Account
- IAM roles with Glue & S3 access
- Basic knowledge of SQL & Python

### Steps
1. Create S3 bucket (bronze/silver/gold)
2. Upload sample data
3. Run Glue Crawler
4. Verify tables in Data Catalog
5. Create Glue ETL job
6. Add transformations (PySpark)
7. Schedule job or trigger via Lambda

---

## 📂 Repository Structure

```
AWS-glue/
│
├── scripts/           # PySpark ETL scripts
├── notebooks/         # Glue notebooks
├── data/              # Sample datasets
├── workflows/         # Orchestration configs
└── README.md
```

---

## 🎯 Learning Outcomes

- Built scalable serverless ETL pipelines
- Implemented incremental data processing
- Applied partitioning for performance optimization
- Automated workflows using Lambda & Glue Workflows
- Gained hands-on experience with PySpark in AWS

---

## 🔗 Repository Link

https://github.com/junaidbilal5/AWS-glue

---

## 📌 Author

**Junaid Bilal**  
Senior Data Engineer | Azure & AWS | PySpark | SQL | Data Pipelines

---

⭐ If you find this project useful, feel free to star the repo!

