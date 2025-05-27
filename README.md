# CovidCanadaETL
CanadaCovidADFPipeline

A data engineering project using Azure Data Factory to process COVID-19 data from Kaggle, load it into an Azure SQL Database, and transform it to focus on Canadian data using Data Flows. This project demonstrates ETL (Extract, Transform, Load) skills, cloud-based data processing, and a focus on Canadian public health data, relevant for data engineering roles.

## Project Overview
This project builds an ETL pipeline in Azure Data Factory to:
1. Ingest COVID-19 data (CSV files) from Kaggle, stored in Azure Data Lake Storage.
2. Use a **Copy Activity** to transfer the data to an Azure SQL Database.
3. Apply transformations in a **Data Flow** to rename columns and filter for Canada-specific data.
4. Store the transformed data in a new SQL table for downstream analysis (e.g., visualization in Tableau).

The pipeline showcases skills in Azure Data Factory, SQL, ETL processes, and data transformation, tailored to a Canadian context.

## Architecture
The pipeline follows a standard ETL architecture, as shown in the diagram below (created using Draw.io):

![Pipeline Architecture](docs/architecture-diagram.png)

### Workflow
1. **Source**: COVID-19 dataset (CSV files) from Kaggle, stored in Azure Data Lake Storage Gen2.
2. **Copy Activity**: Transfers CSV data to a pre-created table in Azure SQL Database.
3. **Data Flow**: Renames columns and filters for Canadian data.
4. **Output**: Stores the filtered Canadian COVID data in a new Azure SQL Database table.

## Tools and Technologies
- **Azure Data Factory**: For orchestrating the ETL pipeline, including Copy Activity and Data Flow.
- **Azure Data Lake Storage Gen2**: Stores the raw Kaggle CSV files.
- **Azure SQL Database**: Stores raw and transformed data.
- **Kaggle**: Source of the COVID-19 dataset.
- **Draw.io**: Used to create the architecture diagram.
- **(Planned) Tableau**: For visualizing the Canadian COVID data (in progress).

## Linked Services
The pipeline uses the following linked services in Azure Data Factory:

1. **Azure Data Lake Storage Gen2**:
   - **Name**: `LS_AzureDataLakeStorage`
   - **Description**: Connects to the Azure Data Lake Storage Gen2 account where Kaggle CSV files are stored.
   - **Configuration**:
     - Type: Azure Data Lake Storage Gen2
     - Authentication: Managed Identity or Service Principal (ensure credentials are securely stored in Azure Key Vault).
     - URL: `https://<storage_account_name>.dfs.core.windows.net/`
     - Container: `covid-data` (example path: `/covid-data/kaggle/covid19.csv`)

2. **Azure SQL Database**:
   - **Name**: `LS_AzureSQLDatabase`
   - **Description**: Connects to the Azure SQL Database hosting the raw and transformed data tables.
   - **Configuration**:
     - Type: Azure SQL Database
     - Authentication: SQL Authentication or Azure AD (use Azure Key Vault for credentials).
     - Server: `<server_name>.database.windows.net`
     - Database: `CovidDB`
     - Tables:
       - `RawCovidData`: Stores unprocessed data from Copy Activity.
       - `CanadaCovidData`: Stores transformed data (filtered for Canada).

3. **Data Flow Linked Service**:
   - **Name**: Implicitly uses the ADF Integration Runtime (`AutoResolveIntegrationRuntime`).
   - **Description**: The Data Flow activity leverages the Azure Integration Runtime for transformations (no separate linked service needed).
   - **Configuration**:
     - Integration Runtime: Default or custom Azure IR (e.g., `AzureIR_CanadaRegion` for Canada-based processing).
     - Data Flow Details:
       - **Source**: `RawCovidData` table in Azure SQL Database.
       - **Transformations**:
         - Rename Columns: Standardizes column names (e.g., `date_reported` to `Date`, `cases` to `DailyCases`).
         - Filter: Selects rows where `country = 'Canada'`.
       - **Sink**: `CanadaCovidData` table in Azure SQL Database.

## Repository Structure
<pre><code> CanadaCovidADFPipeline
├──/dataflow
├──/dataset
├──/docs
   └── diagrams.png
├──/factory
├──/linkedService
│   └── architecture-diagram.png  # Draw.io diagram of the pipeline
├──/pipeline
├── README.md                 # This file
<code><pre>

## Setup Instructions
1. **Prerequisites**:
   - Azure subscription with Data Factory, Data Lake Storage Gen2, and SQL Database.
   - Kaggle COVID-19 dataset (download from [Kaggle](https://www.kaggle.com/datasets)).
   - Draw.io for viewing/editing the architecture diagram.

2. **Steps**:
   - **Data Preparation**:
     - Upload the Kaggle COVID CSV files to Azure Data Lake Storage (`/covid-data/kaggle/`).
   - **Azure SQL Database**:
     - Create a database (`CovidDB`) and tables (`RawCovidData`, `CanadaCovidData`) using `/sql/create_tables.sql`.
   - **Azure Data Factory**:
     - Create linked services (`LS_AzureDataLakeStorage`, `LS_AzureSQLDatabase`) in ADF.
     - Import the pipeline from `/src/pipeline.json` or recreate it:
       - **Copy Activity**: Source (Data Lake CSV) → Sink (RawCovidData table).
       - **Data Flow**: Source (RawCovidData) → Transformations (Rename Columns, Filter Canada) → Sink (CanadaCovidData).
     - Run the pipeline manually or set a trigger (e.g., daily schedule).
   - **Verify Output**: Query the `CanadaCovidData` table in Azure SQL Database to confirm Canadian data.

3. **Optional (In Progress)**:
   - Connect the `CanadaCovidData` table to Tableau for visualization (e.g., dashboards showing Canadian COVID trends).
   - Add error handling (e.g., Azure Logic Apps for alerts) or Python scripts for additional analytics.

## Future Enhancements
- Integrate Tableau to create visualizations of Canadian COVID data (e.g., case trends by province).
- Add error handling and logging for pipeline failures.
- Incorporate Python or Azure Databricks for advanced analytics (e.g., calculating case growth rates).
- Optimize the pipeline with partitioning or indexing for scalability.

## Motivation
This project was developed to demonstrate data engineering skills, with a focus on Azure Data Factory, ETL processes

## Contact
Feel free to reach out via [LinkedIn](https://www.linkedin.com/in/your-profile) or [email](mailto:your-email@example.com) for feedback or collaboration. Check out my progress on X: #DataEngineering #Azure #CanadaTechJobs.

---
*Last Updated: May 27, 2025*
