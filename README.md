# BeeHaven Data Platform: End-to-End Azure Data Engineering 🐝

This repository contains the full implementation of the **BeeHaven Data Platform**, a modern data lakehouse architecture built on Microsoft Azure. The project demonstrates a production-grade ETL/ELT workflow using the **Medallion Architecture** to transform raw data into actionable business insights.

## 🏛️ Architecture Overview

The platform follows the **Medallion (Bronze/Silver/Gold) Design Pattern**, ensuring data quality as it moves through the layers:

* **Bronze Layer**: Raw data ingestion from source blobs.
* **Silver Layer**: Data cleansing, schema enforcement, and transformation using Spark.
* **Gold Layer**: Aggregated, business-ready datasets optimized for analytics.



## 🛠️ Technology Stack

* **Orchestration**: Azure Data Factory (ADF)
* **Compute**: Azure Synapse Analytics (Apache Spark Pools)
* **Storage**: Azure Data Lake Storage Gen2 (ADLS)
* **Environment**: Azure Resource Manager (ARM)

---

## 🚀 Pipeline Workflows

### 1. Master Orchestrator: `DailyHiveProcessing`
The top-level pipeline handles the end-to-end dependency logic:
1.  **Ingestion**: Triggers the `BronzeToSilver` child pipeline.
2.  **Validation**: A 3-minute `Wait` activity ensures file consistency and resource availability between compute jobs.
3.  **Promotion**: Triggers the `SilverToGold` child pipeline to finalize business tables.

### 2. Processing Tiers
* **BronzeToSilver**: Uses a `ForEach` loop and `Get Metadata` activities to dynamically process incoming files. It leverages Synapse Spark notebooks to clean data and moves files from landing zones to storage using `Copy Data` activities.
* **SilverToGold**: Executes high-performance Spark jobs (`ProcessFilesInGold`) to perform joins and aggregations, followed by automated cleanup of temporary silver-tier artifacts.

## 📂 Data Lake Structure
The storage account `beehavenlake` is organized as follows:
* `/bronze`: Raw files and immutable history.
* `/silver`: Cleaned and structured Parquet/Delta files.
* `/gold`: Aggregated business views.
* `/synapse`: Spark system files and warehouse artifacts.

## 📊 Monitoring & Governance
We use the **Synapse Monitor Dashboard** to track Spark application health. 
* **Spark Pool**: `Beehavenspark`
* **Job Metrics**: Monitoring for successful sessions, failure retries, and execution durations (averaging 4-6 minutes for silver-tier processing).

---

## 🔧 Deployment Instructions

1.  **Prerequisites**: An active Azure Subscription and a Resource Group named `bee-haven`.
2.  **Storage Setup**: Create the `beehavenlake` container with the folder hierarchy defined above.
3.  **ADF Configuration**: Import the JSON pipeline definitions from the `/pipelines` directory of this repo.
4.  **Synapse Setup**: 
    * Provision an Apache Spark pool named `Beehavenspark`.
    * Upload the `.ipynb` notebooks located in the `/notebooks` folder.
5.  **Access Control**: Ensure the Data Factory Managed Identity has `Storage Blob Data Contributor` access to the storage account.

---
*Developed as part of the BeeHaven Data Engineering Project.*
