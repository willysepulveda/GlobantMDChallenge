# Globant Data Management and Reporting API

This project implements a solution for **data migration**, **transaction management**, and **report generation** using a set of FastAPI-based services integrated with Azure for data storage and management. Below is a description of each file in the project and its function.

## Project Structure

### Project Files

- **`api_datamanagement_gc.py`**: This file contains the classes and methods for **data backup and restoration** in AVRO format. It includes the `DataBackup` class, which enables backing up selected tables to Azure Blob Storage, and `DataRestore`, which restores data from an AVRO file stored in Blob Storage back to the database. It uses `fastavro` and `BlobServiceClient` from Azure for data serialization and storage.

- **`api_reporting_gc.py`**: This file defines the reporting service, enabling specific metric queries and reports. The `APIReportingGC` class includes methods to:
  - Get the number of employees hired by department and job, organized by quarter.
  - List departments that hired more employees than the yearly average.
  
  These methods query predefined views in the database and return the organized results.

- **`api_transactional_gc.py`**: This file handles **data validation and insertion** into the database. The `DataValidator` class checks that transaction data meets the business rules, while `DataInserter` is responsible for inserting validated records into the corresponding tables. It also includes a **transaction error logging** system to log transactions that don’t meet requirements.

- **`database_connection.py`**: This file configures the **SQL database connection** using `pyodbc` and manages access credentials. If the environment is set to Azure, it retrieves credentials from `Azure Key Vault` using `DefaultAzureCredential` and `SecretClient`. Otherwise, it uses local credentials.

- **`function_app.py`**: This is the main FastAPI application file that defines the **core API endpoints**:
  - `/InsertData`: Inserts validated data into the database.
  - `/BackupData`: Performs backups of specified tables.
  - `/RestoreData`: Restores tables from a backup.
  - `/EmployeeHiresByQuarter`: Queries hires by quarter.
  - `/DepartmentsAboveAverage`: Queries departments with above-average hiring.

  Each endpoint is designed to handle specific operations, with error logging and data validation&#8203;:contentReference.

- **`function.json`**: Defines the **input and output configurations** for the Azure function, specifying that the HTTP trigger (`httpTrigger`) allows `GET` and `POST` methods and sets the authentication level.

- **`host.json`**: General host configuration for the Azure function, including **Application Insights** settings for logging and telemetry sampling, which is useful for monitoring the application in production.

- **`local.settings.json`**: Contains the **environment variables** required to run the application locally, including database credentials, Azure Blob Storage, and Key Vault configurations. This file should not be shared publicly as it contains sensitive information.

- **`requirements.txt`**: Lists the **project dependencies** required to run the application. It includes `FastAPI`, `uvicorn` to run the server, and Azure libraries like `azure-identity`, `azure-keyvault-secrets`, and `azure-storage-blob` for Azure service integration

## Data Flow Diagram

![Data Flow Diagram](./ADFPipeline.png)

## Azure Data Factory Configuration

The following **Azure Data Factory** pipeline is used to load and transform employee, department, and job data:

![ADF Pipeline](./ADFDataFlow.png)

The ADF data flow consists of the following stages:

1. **Import from CSV Files**: Data is read from files in an Azure Blob Storage container.
2. **Null Value Imputation**: Data is cleaned, and null values are imputed.
3. **Load to Database**: The data is stored in the respective SQL tables.

---

This `README.md` file provides an overview of the project components and their functionality. Refer to the source code and configuration files for more details on the implementation.
