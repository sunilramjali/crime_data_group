# UK Land Registry Paid Price Data — Cleaning Rules

## Objective
Create a clean residential house price enrichment dataset for crime analytics.

Final reporting grain:

lsoa × month × crime_category

House price data should therefore be aggregated to:

lsoa × month

---

# Recommended Columns to Keep

Keep only operationally useful fields:

- price
- date_of_transfer
- postcode
- property_type
- ppd_category_type

Drop unnecessary address fields such as:
- street
- town_city
- district
- county
- paon/saon

Reason:
LSOA becomes the primary geographic key after postcode mapping.

---

# Postcode to LSOA Mapping

Use ONS Postcode Directory (ONSPD) to map:

postcode → lsoa_code

This creates a compatible geography with:
- crime data
- deprivation data
- population data

Avoid joining on:
- town names
- district names
- county names

Use:
- lsoa_code

---

# Residential Filtering Rules

Keep only residential property types:

- D = Detached
- S = Semi-detached
- T = Terraced
- F = Flat

Remove:
- O = Other

Reason:
“O” often contains:
- car parks
- garages
- commercial-linked sales
- land-only transactions

---

# Transaction Filtering

Keep only standard sales:

ppd_category_type == "A"

Reason:
Exclude anomalous or non-standard transactions.

---

# Price Filtering

Remove unrealistic low-value transactions:

price >= 50000

Reason:
Low values are often:
- parking spaces
- ownership transfers
- legal corrections
- non-market transactions

---

# Aggregation Metrics

Aggregate to:

lsoa × month

Recommended metrics:
- median_house_price
- transaction_count

Use median instead of average to reduce outlier distortion.

---

# Pipeline Design

## Bronze
Store raw Land Registry data unchanged.

## Silver
Apply:
- column cleaning
- residential filtering
- postcode standardisation
- postcode → LSOA mapping
- deduplication
- validation checks

## Gold
Create BI-ready aggregated outputs:
- monthly median prices
- transaction counts
- enrichment-ready datasets

---

# Validation Checks

Include:
- missing postcode matches
- duplicate detection
- row count validation
- null checks
- temporal alignment validation

---

# Engineering Principles

Prioritise:
- stable enrichment features
- scalable joins
- compatible geographic grain
- BI-ready outputs
- maintainable schemas

Avoid:
- unnecessary columns
- string-based geographic joins
- noisy operational transactions
- many-to-many joins