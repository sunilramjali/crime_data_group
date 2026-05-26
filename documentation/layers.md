Your notebook(s) must be structured as a clear, repeatable pipeline with the following
stages:
Ingestion layer
• Read data in batches or chunks (e.g. by file, time period, or police force)
• Avoid loading the full dataset into memory in a single step without justification
• police forces: Cambridgeshire Constabulary, Avon and Somerset Constabulary, Merseyside Police, Nottinghamshire Police (Scalabale, so can include more police force in the future)

Page 2 of 6
Cleaning & validation layer
• Data quality assessment
• Handle missing values, duplicates, and inconsistent category values
• Implement explicit validation checks, including:
o Row counts before and after transformations
o Duplicate checks at the intended reporting grain
Feature engineering & transformation
• Derive time attributes (e.g. year, month)
• Standardise crime category fields
• Join 1–3 enrichment datasets, ensuring:
o Compatible grain
o No unintentional “many to many” joins
o Clear handling of temporal mismatches (e.g. annual population vs monthly
crime)
Aggregation for reporting
• Aggregate the data to a single, consistent reporting grain
• Prepare metrics required for BI consumption (e.g. crime counts, rates)
Export layer
• Export one final reporting ready dataset
• Ensure the pipeline runs end to end from raw data to final output
• Validate the processes by making a generic analysis on high level statistics (e.g. total
rows, number of regions, etc.)