# 🚀 Metadata-Driven Incremental Data Pipeline (Azure Data Factory)

## 📌 Overview

This project implements a **metadata-driven incremental data ingestion pipeline** using **Azure Data Factory (ADF)**.
It dynamically processes multiple SQL tables, extracts only **new or updated records**, and loads them into a target system using a **watermark-based approach**.

The design is scalable, reusable, and follows real-world enterprise ETL patterns.

---

## 🏗️ Architecture

**Source (SQL Server) → ADF Pipeline → Self-hosted Integration Runtime → Target (Data Warehouse / ADLS / SQL)**

### Key Components:

* Azure Data Factory Pipelines
* Self-hosted Integration Runtime (SHIR)
* Metadata Table (controls pipeline behavior)
* Watermark Logic (incremental loading)

---

## ⚙️ Features

* ✅ Metadata-driven pipeline (no hardcoding)
* ✅ Incremental data load using watermark column
* ✅ Dynamic table processing using ForEach
* ✅ Lookup-based configuration handling
* ✅ Error handling and monitoring
* ✅ Supports Parquet/SQL-based ingestion
* ✅ Scalable to multiple tables

---

## 🗂️ Project Structure

### 1. Metadata Table

Stores configuration for each table:

| Column Name     | Description                       |
| --------------- | --------------------------------- |
| TableName       | Source table name                 |
| WatermarkColumn | Column used for incremental load  |
| LastLoadValue   | Last processed value              |
| IsActive        | Enable/Disable pipeline for table |

---

### 2. Pipelines

#### 🔹 Main Pipeline

* Reads metadata table
* Iterates over tables using **ForEach**

#### 🔹 Lookup Activities

* Fetch table list
* Fetch max watermark value from source

#### 🔹 Conditional Logic

* Checks if new data exists:

```sql
NewMaxValue > LastLoadValue
```

#### 🔹 Copy Activity

* Loads incremental data into target

---

### 3. Incremental Logic

```sql
SELECT *
FROM SourceTable
WHERE WatermarkColumn > @LastLoadValue
```

---

### 4. Watermark Update

After successful load:

* Update metadata table with new max value

---

## 🔄 Execution Flow

1. Lookup metadata table
2. ForEach loop for each table
3. Fetch max watermark value
4. Compare with last processed value
5. If new data exists → trigger copy
6. Update watermark

---

## 🛠️ Technologies Used

* Azure Data Factory (ADF)
* SQL Server
* Self-hosted Integration Runtime
* Parquet / Azure Data Lake (optional)
* Java (required for Parquet handling)

---

## ⚠️ Key Challenges & Fixes

### 1. Lookup Output Issue

* Problem: `value` vs `firstRow`
* Fix: Align with **First Row Only** setting

### 2. Java Runtime Error

* Issue: `jvm.dll not found`
* Fix:

  * Install Java (JDK 8)
  * Set `JAVA_HOME`
  * Restart Integration Runtime

---

## 📊 Monitoring

* Use **ADF Monitor Hub**
* Track:

  * Pipeline runs
  * Activity runs
  * Execution duration
  * Failures

---

## 🚀 Future Enhancements

* Add retry mechanism
* Implement logging table (audit)
* Add email/alert notifications
* Support CDC (Change Data Capture)
* Integrate with Azure Synapse / Databricks

---

## 💡 Key Learnings

* Difference between `firstRow` and `value` in ADF
* Importance of Integration Runtime configuration
* Real-world implementation of incremental pipelines
* Metadata-driven design pattern

---

## 👨‍💻 Author

Rahul K
Azure Data Engineering 

---
