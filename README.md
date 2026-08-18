# azure-secure-data-validation-pipeline
# Secure Enterprise Data Ingestion & Validation Pipeline
## 📌 Project Overview
An end-to-end automated data engineering pipeline built on Azure that securely ingests, validates, and routes incoming operational data based on schema compliance and deduplication rules. This architecture implements a production-grade security-first framework and a Medallion-style storage lifecycle.
## 🛠️ Architecture & Core Components
* **Orchestration**: **Azure Data Factory (ADF)** managing event-driven triggers to automate processing upon file arrival.
* **Compute & Validation Layer**: **Azure Databricks (PySpark)** executing complex schema validation algorithms and historical duplication checks.
* **Security & Governance**: **Azure Key Vault** managing secure access tokens, service principals, and database connection strings via `dbutils.secrets`.
* **Storage Layers**: **Azure Data Lake Storage Gen2 (ADLS Gen2)** configured with a hierarchical namespace to segment data into landing, accepted (clean), and rejected (quarantine) zones.
## 🔄 Data Pipeline Workflow
1. **Event Trigger**: A new raw CSV dataset arrives in the landing zone container of ADLS Gen2, automatically triggering the ADF ingestion pipeline.
2. **Secure Authentication**: Databricks initializes a secure compute session, fetching storage access tokens directly from Azure Key Vault to eliminate hardcoded credentials.
3. **Quality & Schema Enforcement**: The PySpark validation engine cross-references the incoming file against defined data types, null constraints, and schema structures.
4. **Deduplication Check**: Data is scanned for duplicate records across critical business keys using optimized dataframes.
5. **Conditional Routing**:
  * **Passed Records**: Clean, compliant data is safely written to the `/accepted` production directory.
  * **Failed Records**: Rows violating schema validation or duplicate checks are isolated and routed to the `/rejected` quarantine directory for support team diagnostics.
## 📈 Key Engineering Takeaways
* **Zero-Trust Security**: No connection strings or passwords live in the source code; everything is fully abstracted into Azure Key Vault.
* **Idempotency & Resilience**: The pipeline handles reprocessing seamlessly by isolating duplicates before hitting the core analytical layers.
* **Operational Monitoring**: Designed with clear accepted/rejected data paths to simulate corporate troubleshooting environments.
