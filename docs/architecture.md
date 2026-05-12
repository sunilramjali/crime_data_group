Create a clean architecture diagram for a UK Police Crime Data Pipeline project.

Title:
UK Police Crime Data Pipeline Architecture

Subtitle:
Databricks + Snowflake + Power BI

The diagram should show a left-to-right data flow with 5 main sections:

1. DATA SOURCES
Show the primary dataset:
- UK Police Crime Data
- 4 selected police forces

Show enrichment datasets:
- Population data
- House price data
- Deprivation

Add note:
All enrichment datasets are publicly available and processed using Python.

2. DATABRICKS / PYSPARK ETL LAYER
Show Databricks as the main ETL and processing layer.

Include these stages inside Databricks:
- Ingestion layer
  - Read files in batches or chunks
  - Iterate by file, month, or police force
  - Store raw data in Bronze layer

- Cleaning and validation layer
  - Standardise column names
  - Handle missing values
  - Remove duplicates
  - Standardise crime categories
  - Validate row counts before and after transformations
  - Check duplicates at reporting grain

- Feature engineering and transformation layer
  - Derive year and month
  - Standardise date fields
  - Create join keys
  - Prepare data for enrichment

- Enrichment layer
  - Join population, house price, and deprivation datasets
  - Ensure compatible grain
  - Prevent many-to-many joins
  - Handle temporal mismatches

- Aggregation layer
  - Aggregate to one reporting grain:
    lsoa × month × crime_category
  - Calculate crime_count
  - Calculate crime_rate_per_1,000_residents
  - Create BI-ready output

Also show Databricks storage layers:
- Bronze: raw data
- Silver: cleaned and enriched data
- Gold: aggregated BI-ready data

3. SNOWFLAKE DATA WAREHOUSE
Show Snowflake receiving the Gold layer data from Databricks.

Include:
- Data loading from Databricks to Snowflake
- Warehouse storage
- Star schema data model

Show example tables:
- fact_crime
- dim_force
- dim_date
- dim_crime_type
- optional dim_area

The fact_crime table should contain:
- force_id
- date_id
- crime_type_id
- crime_count
- population
- crime_rate_per_1k

Include reporting views / semantic layer:
- BI-ready SQL views
- Business-friendly aggregations
- Secure and scalable reporting layer

4. POWER BI ANALYTICS
Show Power BI connected to Snowflake.

Include dashboard outputs:
- Overall crime trends
- Crime category analysis
- Police force comparisons
- Normalised crime rates
- Seasonal patterns
- Geographic analysis
- KPI dashboards

Show users:
- Police leadership
- Analysts
- Operational teams
- Decision makers

5. GOVERNANCE, QUALITY AND MONITORING
Place this as a horizontal layer underneath the full pipeline.

Include:
- Data quality checks
- Validation at each layer
- Logging and monitoring
- Error handling
- Documentation
- Data dictionary
- Assumptions and limitations
- Version control with Git/GitHub
- Security and access control

VISUAL STYLE
Use a clean modern data engineering architecture style.
Use separate coloured boxes for:
- Data Sources
- Databricks ETL
- Snowflake Warehouse
- Power BI Analytics
- Governance

Use arrows to show movement from:
Data Sources → Databricks → Snowflake → Power BI