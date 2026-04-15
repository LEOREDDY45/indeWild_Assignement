# indeWild_Assignement
First Round for IndeWild assignment. 
This project implements a Bronze-Silver-Gold data pipeline using Azure Databricks and ADLS Gen2 to process and unify multi-source e-commerce data.
Infrastructure Setup
Storage: RAW CSV files were uploaded to Azure Data Lake Storage (ADLS) Gen2 with standardized filenames.
Connectivity: Used Azure Access Connector (Managed Identity) with "Storage Blob Data Contributor" roles instead of traditional mounting for better security and ease of use.
Configuration: Created a modular config notebook using %run to define path variables (Bronze/Silver/Gold) for consistent environment access.
Data Pipeline
1. Bronze to Silver (Ingestion & Cleaning)
Myntra Data: Read using inferSchema to handle complex Excel-formatted numbers.
Other Sources: Applied manual schema definitions for consistency.
Standardization:
Renamed fields: saled → total_units, mrp_revenue → total_revenue.
Converted strings to date objects using to_date().
Added a data_source tag to every record.
Dropped null values and selected only essential columns.
2. The Identity Challenge
Since there was no common primary key across the four sources, I used a fuzzy matching approach to create a Product Master:
Extracted all distinct SKU names from the Silver layer.
Mapped similar product names (70%+ similarity) to a unique product_identifier in snake_case.
Created a mapping DataFrame to act as a "Source of Truth" for joining disparate datasets.
3. Silver to Gold (Transformation & Loading)
Joins: Performed a broadcast join between the Silver tables and the Mapping DataFrame to assign unique IDs.
Aggregation: Combined all processed datasets using unionByName.
Validation: Ensured all schemas and data types matched across sources to prevent column scrambling during the final append.
Output: Loaded the final unified dataset into the Gold layer for reporting.




