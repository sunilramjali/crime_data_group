This is a prompt for cleaning and validation layer of the pipeline of adi, houseprices and lsoa_postcode

Look at adi_preprocesing.ipynb, houseprices.ipynb, lsoa_poscode.ipynb

Make modular, and scalable

Cleaning & validation layer
• Data quality assessment
• Handle missing values, duplicates, and inconsistent category values
• Implement explicit validation checks, including:
o Row counts before and after transformations
o Duplicate checks at the intended reporting grain

Output parquest file to be ouput in data/silver