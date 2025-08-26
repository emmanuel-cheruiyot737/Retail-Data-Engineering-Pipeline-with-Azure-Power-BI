# Retail Data Engineering Project Documentation
# 📌 Project Overview

![Architure](https://github.com/emmanuel-cheruiyot737/killer-boy-data-enginneering/blob/main/cherry_proj1.png) This project implements an **end-to-end retail data pipeline** using Azure Data Engineering tools such as **Azure Data Factory (ADF), Azure Data Lake Storage(ADLS), Azure Databricks and Power BI.** 
The pipeline ingests data from multiple sources, transforms it into a structured **data lake format (Bronze → Silver → Gold layers),** and enables interactive **business reporting and analytics** through Power BI dashboards.

---

## Tech Stack & Azure Services Used

### **🔹Tech Stack**

- **Python (PySpark) –** Data cleaning, transformations, aggregations

 - **SQL –** Source database schema & queries

 - **JSON / API –** Customer master data ingestion

 - **Delta Lake –** Optimized storage format for Silver & Gold layers

 - **Power BI –** Interactive dashboards & reporting

### **🔹 Azure Services Used**

- **Azure Data Factory (ADF) →** Orchestration Data ingestion from SQL DB & API

- **Azure Data Lake Storage (ADLS) →** Central data lake with Bronze, Silver, Gold zones

- **Azure Databricks →** Data cleaning, transformation, aggregation and data engineering(ETL pipeline with PySpark)

- **Azure SQL Database →** Source system for transactions, products, and stores

- **Power BI** → Business intelligence, visualization, Reporting & dashboards

---

## 📂 Data Sources

**1. Azure SQL Database**

 - **Transactions** (sales data)

 - **Stores** (store details)

- **Products** (catalog details)

**2. API / JSON**

- **Customers** (customer master data in JSON format)

---

## 🏗️ Architecture
```
graph LR
A[Azure SQL - Transactions] --> B[ADF]
C[Azure SQL - Stores] --> B
D[Azure SQL - Products] --> B
E[API - Customers JSON] --> B
B --> F[ADLS - Bronze]
F --> G[Databricks - Silver Layer]
G --> H[Databricks - Gold Layer]
H --> I[Power BI Reports]
```
---

- **Bronze Layer →** Raw ingestion from SQL & JSON

- **Silver Layer →** Cleaned, standardized, joined dataset

- **Gold Layer →** Aggregated business-level KPIs for reporting

## ⚙️ Data Processing
**Bronze Layer**

- Raw data files store in ADls from SQL DB & JSON API

- File format: Parquet(SQL data) & JSON/Parquet(customers)

**Silver Layer**

- Cleaned using PySpark (```retail projects - multiple tables (1).py```):

- Schema alignment (casting datatypes)

- Removing duplicates

- Joining customers, stores, products, transactions

- Adding derived column: ```total_amount = quantity * price```

**Gold Layer**

- Aggregated metrics created:

- total_quantity_sold

- total_sales_amount

- number_of_transactions

- average_transaction_value

Stored as **Delta tables** for optimized analytics.

---

## 📊 Business Requirements & KPIs
**1. Total Sales by Store and Category**

- **Metric:** SUM(total_sales_amount)

- **Dimensions:** Store, Category

- **Visualization:** Grouped Bar Chart

**2. Daily Sales Trend by Product**

 -**Metric:** SUM(total_sales_amount)

 - **Dimensions:** Date, Product

 - **Visualization:** Line Chart (time series)

**3. Average Order Value per Store**

 - **Metric:** ```SUM(total_sales_amount) / COUNT(transaction_id)```

 - **Visualization:** Column Chart (per store)

**4. Heatmap: Store vs Sales**

- **Metric:** SUM(total_sales_amount)

- **Dimensions:** Store × Product/Category

- **Visualization:** Heatmap (intensity by sales)

---  

## 📑 Power BI Reporting

- Connect Power BI to **Gold Layer Delta tables**

- Create dashboard with:

  - 📊 Sales by Store & Category

  - 📈 Daily Sales Trend by Product

  - 💰 AOV per Store

  - 🔥 Heatmap: Store vs Sales

## 🚀 Deployment Steps

**1.** Deploy SQL schema & sample data (```SCRIPT SQL.txt```)

**2.** Upload ```customers.json``` to API/Blob for ingestion

**3.** Configure ADF pipelines:

 - Copy data from SQL & JSON to ADLS Bronze

**4.** Run Databricks notebook (```retail projects - multiple tables (1).py```)

 - Create Silver & Gold tables

**5.** Connect Power BI → Gold Layer Delta tables

Build dashboards as per KPIs

---

## 📜 Code & Notebooks

**1. SQL Script →** ```SCRIPT SQL.txt```

- Creates source tables (products, stores, transactions) and inserts sample   data.

**2. JSON Data →** ```customers.json```

 - Provides sample customer master data for API ingestion.

**3. Databricks Notebook →**  ```retail projects - multiple tables (1).py```

- Implements ETL pipeline:

- Ingests Bronze data

- Cleans and standardizes into Silver

- Aggregates business KPIs into Gold

---

## 🖥️ PySpark ETL Highlights

**Load Bronze Data:**

```
df_transactions = spark.read.parquet('/mnt/retail_project/bronze/transaction/')
df_products = spark.read.parquet('/mnt/retail_project/bronze/product/')
df_stores = spark.read.parquet('/mnt/retail_project/bronze/store/')
df_customers = spark.read.parquet('/mnt/retail_project/bronze/customer/')

```

**Transform into Silver:**

```

from pyspark.sql.functions import col

df_transactions = df_transactions.select(
    col("transaction_id").cast("int"),
    col("customer_id").cast("int"),
    col("product_id").cast("int"),
    col("store_id").cast("int"),
    col("quantity").cast("int"),
    col("transaction_date").cast("date")
)


df_customers = df_customers.select(
    "customer_id", "first_name", "last_name", "email", "city", "registration_date"
).dropDuplicates(["customer_id"])
```

**Join All Data (Silver):**

```
df_silver = df_transactions \
    .join(df_customers, "customer_id") \
    .join(df_products, "product_id") \
    .join(df_stores, "store_id") \
    .withColumn("total_amount", col("quantity") * col("price"))
```

**Gold Aggregates:**

```
from pyspark.sql.functions import sum, countDistinct, avg


gold_df = silver_df.groupBy(
    "transaction_date", "product_id", "product_name", "category",
    "store_id", "store_name", "location"
).agg(
    sum("quantity").alias("total_quantity_sold"),
    sum("total_amount").alias("total_sales_amount"),
    countDistinct("transaction_id").alias("number_of_transactions"),
    avg("total_amount").alias("average_transaction_value")
);
```
---
## 📊 Power BI Dashboards

---

## 🔐 Security & Governance

- **Data Security:** Enforce **RBAC** on ADLS and Databricks access.

- **Data Encryption:** Ensure data is encrypted at rest (ADLS, SQL) and in transit (HTTPS/SSL).

- **Data Governance:** Integrate with Azure Purview for data catalog, lineage, and classification.

- **Power BI:** Apply **Row-Level Security (RLS)** for controlled data visibility.

---

## 📦 Repository Structure

```

├── customers.json                  # Customer data (JSON)
├── SCRIPT SQL.txt                  # SQL schema + inserts
├── retail projects - multiple tables (1).py   # Databricks ETL script
├── docs/
│   ├── architecture.png            # Architecture diagram
│   └── dashboard_wireframes.png    # Power BI mockups
└── README.md                       # Project documentation

```

---

## 📈 Results & Insights

- Identified top-performing stores and categories by sales

- Analyzed daily sales trends across products

- Measured average order value per store to assess revenue efficiency

- Visualized store vs sales heatmap to spot regional strengths

## 📌 Future Enhancements

- Automate pipeline scheduling with ADF triggers

- Add incremental data loads (CDC for transactions)

- Implement role-based security in Power BI

- Integrate ML models for demand forecasting

- Extend Gold Layer to Azure Synapse for enterprise warehousing
---
