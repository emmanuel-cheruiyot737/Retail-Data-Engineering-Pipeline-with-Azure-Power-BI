# tokyo-olympic-azure-data-engineering-project

## 📌 Project Overview

![project Pipeline](https://github.com/emmanuel-cheruiyot737/azure-data-engineer---multi-source/blob/main/cherry1.png)

This project demonstrates an end-to-end **data engineering pipeline on Microsoft Azure** to analyze the **Tokyo 2021 Olympics datasets**.  

It covers the entire data lifecycle **— ingestion → storage → transformation → analytics → visualization** enabling insights into medal tallies, athlete performance, gender participation, coach/team distribution, and sports evolution.

---

## 🏗️ Tokyo Olympics Data Engineering Architecture

- **Data Source** – Olympic datasets from Kaggle (Athletes.csv, Coaches.csv, EntriesGender.csv, Medals.csv, Teams.csv).

- **Ingestion (Azure Data Factory)** – Automated pipelines for data ingestion, scheduling, and monitoring.

- **Raw Storage (Azure Data Lake Gen2 - Raw Zone)** – Stores unprocessed data for traceability.

- **Transformation (Azure Databricks)** – PySpark notebooks for cleaning, joining, and applying business rules (e.g., medal aggregation, gender distribution, team analysis).

- **Curated Storage (Azure Data Lake Gen2 - Curated Zone)** – Stores structured and analytics-ready datasets.

- **Analytics & Querying (Azure Synapse Analytics)** – Star schema modeling, SQL queries for medal tallies, athlete participation, and country comparisons.

- **Visualization (Power BI / Looker Studio / Tableau)** – Interactive dashboards showing:

  - 🥇 Country medal leaderboards  
  - 👩‍🦱 Athlete demographics (age, gender, sport)  
  - 🏋️ Gender participation by discipline  
  - 🧑‍🤝‍🧑 Team & coach distribution per country  
  - 📈 Sports growth & popularity trends  

---

## 📊 Key Insights Delivered

- Country medal tallies for Tokyo 2021  
- Gender participation across all disciplines  
- Athlete performance by age, sport, and country  
- Coach distribution per sport and country  
- Team participation and size analysis  
- Evolution of Olympic sports popularity  

---

## 📂 Project Workflow

```flowchart LR
A[Data Sources] --> B[Azure Data Factory]
B --> C[Data Lake - Raw Zone]
C --> D[Azure Databricks - PySpark ETL]
D --> E[Data Lake - Curated Zone]
E --> F[Azure Synapse Analytics]
F --> G[Power BI/Tableau Dashboards]
