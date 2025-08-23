# azure-data-engineer---multi-source

## 📌 Project Overview

![project Pipeline](https://github.com/emmanuel-cheruiyot737/azure-data-engineer---multi-source/blob/main/cherry1.png)


This project demonstrates an end-to-end data engineering pipeline on Microsoft Azure to analyze Olympic Games data. It covers the entire data lifecycle **— ingestion → storage → transformation → analytics → visualization** enabling insights into medal tallies, athlete performance, gender participation, and sports evolution over time
Architecture.

---

## The solution follows a modern data engineering architecture on Azure:

- **Data Source** – Olympic datasets (CSV, JSON, APIs, historical repositories).

- **Ingestion (Azure Data Factory)**  – Automated pipelines for data ingestion, scheduling, and monitoring.

- **Raw Storage (Azure Data Lake Gen2 - Raw Zone)** – Stores unprocessed data for traceability.

- **Transformation (Azure Databricks)** – PySpark notebooks for cleaning, joining, and applying business rules (e.g., medal aggregation, athlete demographics).

- **Curated Storage (Azure Data Lake Gen2 - Curated Zone)** – Stores structured and analytics-ready datasets.

- **Analytics & Querying (Azure Synapse Analytics)** – Star schema modeling, SQL queries for medal tallies, athlete performance, and country comparisons.

- **Visualization (Power BI / Looker Studio / Tableau)** – Interactive dashboards showing:

  - 🥇 Country medal leaderboards

  - 👩‍🦱 Athlete demographics (age, gender, sport)

  - 📈 Sports growth & popularity trends

  - 🕒 Olympic history & participation


  
## 📊 Key Insights Delivered

- Country medal tallies across Olympic history

- Gender participation trends over decades

- Athlete performance by age, sport, and country

- Evolution of Olympic sports & popularity trends

  ## 📂 Project Workflow
```flowchart LR
|-- A[Data Sources] --> B[Azure Data Factory]
|-- B --> C[Data Lake - Raw Zone]
|-- C --> D[Azure Databricks - PySpark ETL]
|-- D --> E[Data Lake - Curated Zone]
|-- E --> F[Azure Synapse Analytics]
|-- F --> G[Power BI/Tableau Dashboards]
```
---

## 📂 Repository Structure
```olympic-data-analytics/
├── data/                # Sample datasets (CSV, JSON)
├── notebooks/           # PySpark ETL notebooks
├── pipelines/           # ADF pipeline JSON exports
├── sql/                 # Synapse SQL scripts (star schema, fact/dim tables)
├── dashboards/          # Power BI / Tableau reports
└── README.md            # Project documentation
```
---

## 🛠️ Tech Stack

- **Azure Data Factory** – Data ingestion & orchestration

- **Azure Data Lake Storage Gen2** – Raw & curated zones

- **Azure Databricks (PySpark)** – Data cleaning & transformation

- **Azure Synapse Analytics** – Data modeling & SQL queries

- **Power BI / Tableau / Looker Studio** – Dashboarding & visualization

- **SQL & Python (PySpark)** – ETL & analytics
  
  ---

## 🔑 Prerequisites

- Azure subscription (ADF, ADLS, Databricks, Synapse enabled)

- Databricks cluster configured

- Power BI Desktop / Tableau installed

- Olympic dataset (Kaggle / IOC historical data)

## 📥 Installation & Setup

**1.** Clone the repository:

```bash
git clone https://github.com/username/olympic-data-analytics.git
cd olympic-data-analytics
```

**2.** Deploy **ADF pipelines** using JSON files in ```/pipelines/.```

**3.** Upload raw datasets into **ADLS Raw Zone**.

**4.** Run **PySpark ETL notebooks** in ```/notebooks/```to transform data.

**5.** Execute **SQL scripts** in ```/sql/``` to create fact & dimension tables in Synapse.

**6.** Connect **Power BI** to Synapse to build dashboards.

---

## 🔄 Data Transformation (PySpark ETL in Databricks)
# Load Raw Data from ADLS
```python
athletes_df = spark.read.csv(
    "abfss://raw@<storage_account>.dfs.core.windows.net/athletes.csv",
    header=True, inferSchema=True
)

medals_df = spark.read.csv(
    "abfss://raw@<storage_account>.dfs.core.windows.net/medals.csv",
    header=True, inferSchema=True
)
```
## Data Cleaning & Transformation
```python
from pyspark.sql.functions import col, trim, upper

clean_athletes_df = athletes_df.withColumn("Name", trim(col("Name"))) \
                               .withColumn("Country", upper(col("Country")))

clean_medals_df = medals_df.filter(col("Medal").isin("Gold", "Silver", "Bronze"))
```

## Medal Aggregation by Country
```python
from pyspark.sql.functions import count

country_medals_df = clean_medals_df.groupBy("Country", "Medal") \
                                   .agg(count("*").alias("Total"))

country_medals_df.write.mode("overwrite").parquet(
    "abfss://curated@<storage_account>.dfs.core.windows.net/country_medals"
)
```

## 🗂️ Data Modeling (SQL in Synapse)
### Create Dimension Tables
```sql
CREATE TABLE DimCountry (
    CountryID INT IDENTITY PRIMARY KEY,
    CountryName NVARCHAR(100)
);

CREATE TABLE DimAthlete (
    AthleteID INT IDENTITY PRIMARY KEY,
    Name NVARCHAR(150),
    Gender CHAR(1),
    CountryID INT FOREIGN KEY REFERENCES DimCountry(CountryID)
);
```

### Create Fact Table
```sql
CREATE TABLE FactMedals (
    FactID INT IDENTITY PRIMARY KEY,
    AthleteID INT FOREIGN KEY REFERENCES DimAthlete(AthleteID),
    CountryID INT FOREIGN KEY REFERENCES DimCountry(CountryID),
    Sport NVARCHAR(100),
    Event NVARCHAR(150),
    Medal NVARCHAR(10),
    Year INT
);
```

### Medal Tally Query
```sql
SELECT 
    c.CountryName,
    m.Medal,
    COUNT(*) AS TotalMedals
FROM FactMedals m
JOIN DimCountry c ON m.CountryID = c.CountryID
GROUP BY c.CountryName, m.Medal
ORDER BY TotalMedals DESC;
```
## 📊 Key Insights

- USA, USSR, and China dominate Olympic medal tallies across history.

- Female participation increased 5x from 1960 → 2020.

- Average athlete age varies significantly by sport (e.g., gymnastics vs      marathon).

- Newer sports (e.g., skateboarding, surfing) show rapid growth in            participation.
  
---
 
 ## ✅ Learnings

- Designed and implemented a **Medallion Architecture** (Raw → Curated → Analytics).

- Optimized PySpark jobs for large-scale ETL workloads.

- Applied **star** schema modeling for analytical efficiency in Synapse.

- Improved **data storytelling** with interactive Power BI dashboards.
## 📈 Future Enhancements

- Add real-time ingestion via **Azure Event Hub + Stream Analytics**

- Deploy predictive models (e.g., athlete performance forecasting)

- Automate CI/CD with **GitHub Actions + Azure DevOps**

- Build a centralized **Data Catalog with Purview**
  
## Sample Dashboard

---

(Add screenshots of your Power BI/Tableau dashboards here for visual appeal)

## Skills Demonstrated

- Cloud Data Engineering (Azure ecosystem)

- Data Pipeline Orchestration (ADF)

- Big Data Processing (PySpark, Databricks)

- Data Warehousing & Modeling (Synapse, Star Schema)

- Business Intelligence & Visualization (Power BI, Tableau, Looker Studio)

- SQL Analytics & Optimization

- End-to-End Pipeline Development

  ---

## 📥 Dataset

Source: Olympic Data from Kaggle
 (or IOC APIs / historical repositories)

---

## 🤝 Contributing

Pull requests are welcome! For major changes, please open an issue first to discuss what you’d like to change

---

## 📜 License

This project is licensed under the MIT License.
