# Setup Layer — Unity Catalog Bootstrap

## Purpose

Provisions the Unity Catalog (UC) schemas and volumes that every data notebook depends on.
Run **once per workspace** before executing any other notebook in the pipeline.

All DDL is idempotent (`IF NOT EXISTS`) — safe to re-run without side effects.

---

## Why a Separate Notebook?

UC schemas and volumes are **governance objects** registered in the metastore. They cannot be
created with `os.makedirs` or any filesystem call — only `CREATE SCHEMA` / `CREATE VOLUME` SQL
DDL can do this. Placing the DDL in a dedicated `00_setup.ipynb` keeps the data notebooks
(`01`–`03`) free of infrastructure concerns and makes the one-time provisioning step explicit.

---

## Objects Provisioned

| Object | SQL | Purpose |
|---|---|---|
| Schema `bronze` | `CREATE SCHEMA IF NOT EXISTS crime_data.bronze` | Holds raw Delta tables + volumes |
| Schema `silver` | `CREATE SCHEMA IF NOT EXISTS crime_data.silver` | Holds cleaned/transformed outputs |
| Volume `bronze.landing` | `CREATE VOLUME IF NOT EXISTS crime_data.bronze.landing` | Upload destination for raw CSVs |
| Volume `bronze._meta` | `CREATE VOLUME IF NOT EXISTS crime_data.bronze.\`_meta\`` | Auto Loader checkpoints + schema logs |
| Volume `silver.outputs` | `CREATE VOLUME IF NOT EXISTS crime_data.silver.outputs` | Parquet outputs from layers 02 & 03 |

> **Note:** The `` `_meta` `` identifier requires backtick-quoting because UC identifiers
> starting with `_` must be quoted in SQL.

---

## Storage Layout After Setup

```
catalog: crime_data
├── schema: bronze
│   ├── volume: landing       → /Volumes/crime_data/bronze/landing/
│   │       adi/ADI_<year>/ADI_*.csv
│   │       price_paid/pp-*.csv
│   │       postcode/ONSPD_*.csv
│   └── volume: _meta         → /Volumes/crime_data/bronze/_meta/
│           _schemas/<table>/
│           _checkpoints/<table>/
└── schema: silver
    └── volume: outputs        → /Volumes/crime_data/silver/outputs/
            *_clean.parquet
            *_transformed.parquet
```

---

## Prerequisites

| Requirement | Notes |
|---|---|
| Catalog `crime_data` exists | Creating catalogs requires workspace-admin. Change `CATALOG` in the notebook if your workspace uses a different name (e.g. `main`, `sandbox`). |
| `CREATE SCHEMA` privilege on the catalog | Required to create `bronze` and `silver` schemas. |
| `CREATE VOLUME` privilege on each schema | Required to create the three volumes. |

If the notebook fails with a permissions error, ask a workspace admin to run it once on your
behalf, or ask them to grant you `CREATE SCHEMA` and `CREATE VOLUME` on `crime_data`.

---

## Running the Notebook

1. Open `notebooks/00_setup.ipynb` on any cluster with Unity Catalog enabled.
2. Confirm `CATALOG = "crime_data"` matches your workspace catalog.
3. Run all cells top-to-bottom (≈ 5 seconds).
4. The final cell prints `SHOW VOLUMES` output for both schemas — verify all three volumes appear.
5. Proceed to `01_ingestion_layer.ipynb`.

---

## Downstream

| Notebook | Dependency on setup |
|---|---|
| `01_ingestion_layer.ipynb` | Reads from `bronze.landing` volume; writes to `bronze.*` Delta tables |
| `02_cleaning_and_validation_layer.ipynb` | Reads `bronze.*` tables; writes parquet to `silver.outputs` volume |
| `03_feature_engineering_and_transformation.ipynb` | Reads and writes parquet in `silver.outputs` volume |
