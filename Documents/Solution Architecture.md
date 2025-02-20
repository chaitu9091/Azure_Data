Solution Architecture
=======================
Medalian Architecture
![Storage account](Images/image.png)

Storage Acount
---------------
Under the Storage Account -> Containers there are 
Storage Acccount Creation![ac creation](<Images/Storage AC creation.png>)
![SACC1](<Images/Sacc 1.png>)

Containers
------------
- > Bronze
- > Silver
- > Gold 
- > Landing
- > config - ignore for now

#### Landing --> Bronze --> Silver --> Gold

EMR (SQL DB) 

Claims (Flat Files)

Codes (Parquet Files)


Delta Lake Storage
--------------------
Hierarchical namespace - Enabled will be ADLS gen 2 account

ADLS gen 2 is recommended for Big Data Analytics

Data Landed
-------------
Insurance Provider will upload the files to the Landing layer 
we will try to bring it to the bronze layer 

Process
---------
Landing    -->    Bronze      -->   Silver      -->    Gold
Flat Files --> Paraquet files --> Delta tables  --> Delta tables

Ingest the files from 'FLAT FILES' to 'PARAQUET' Azure Data Factory from Landing to Bronze Layer

- > Bronze - Parquet format (source of truth)
- > Silver - data cleaning, enrich the data, commom data modeling (CDM), Slowing Changing Dimension type 2
- > Gold - aggregation, Fact Table and Dimension Table

- > Gold - Business Users
- > Silver - Data Scientist, Machine Learning, Data Analytics
- > Bronze - Data Engineer

### Each layer serve differ purpose
Bronze layer - has only Parquet Format data

Do the following in the below layer and here the data is in Delta tables and Fact tables.
Silver - data cleaning, enrich the data, commom data modeling (CDM), Slowing Changing Dimension type 2

ADF and Azure Data Bricks are the two most portion
![Fact Table ERD Diagram](Images/ERD.jpeg)

Fact Table - Main Details/ a numeric value (never change)

Dimensions Table - Supporting things (can change) SCD2 is the Industry type modeling

Ingestion
-------------
- > EMR Data - > Bronze Layer

- > Azure Data Factory (For Ingestion)

- > Azure DataBricks for our data processing

- > Azure SQL DB - EMR data 

- > Azure Storage Account - Raw files, Paraquet files 

- > Key Vault - for storing the cerdentials

Azure Storage Account
-----------------------
hcadls (adls gen2)
- > Containers
    - > Landing
    - > Bronze
    - > Silver
    - > Gold
    - > configs (metadata driven pipeline)
        - > EMR -- > load_config.csv
           


Container![SAC](<Images/SA Containers.png>)

EMR (Azure SQL DB) - > ADLS Gen 2 (Bronze folder in parquet format)

We will create a audit table (Delta Table )

We will create a audit table (Delta Table in Data Bricks) for this need a linked service


- > ADF Pipeline

- > Linked Service 
    - > Azure Sql DB - source
    - > ADLS Gen 2 - destination 
    - > Delta Lake 
    - > Key Vault

- > Datasets
    - > Azure Sql DB / Database_name /table_name / schema_name
    - > ADLS Gen 2 / file_name/ file_path / file_format

- > Activities
    
- > Pipeline






