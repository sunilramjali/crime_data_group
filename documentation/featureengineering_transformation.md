for claude - use opus 4.7 to plan
for claude - use sonnet 4.6 to execute(edit)

This is a prompt for feature engineering and transformation layer of the pipeline of adi, houseprices and lsoa_postcode

Look at adi_preprocesing.ipynb, houseprices.ipynb, lsoa_poscode.ipynb

Make modular, and scalable

--------------------------------------
from brief:
Feature engineering & transformation
• Derive time attributes (e.g. year, month)
• Standardise fields
• Join 1–3 enrichment datasets, ensuring:
o Compatible grain
o No unintentional “many to many” joins
o Clear handling of temporal mismatches (e.g. annual population vs monthly crime)
--------------------------------------

- adi calculation (join rate additon, crime, health, and claimant)
- join houseprices and lsoa postcode, remove postcode and aggregate data to lsoa level

input parquet files from cleaning_and_validation_layer

output data/silver as parquest files
- adi_transformed
- houseprices_transformed

Ask any questions if necessary