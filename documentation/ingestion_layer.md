# Ingestion Layer — Databricks (CSV → Bronze Delta)

## Purpose

Reads every source CSV from a Unity Catalog Volume using **Databricks Auto Loader**
(`cloudFiles`) and lands each source into its own **bronze Delta table**. Incremental
by design: re-running only processes files that have not been seen before.

---

## Storage Layout

```
Catalog : crime_data
Schema  : bronze

/Volumes/crime_data/bronze/landing/        ← upload CSVs here, preserving folders
    ADI_2017/
        ADI_claimant_counts_2017.csv
        ADI_crime_2017.csv
        ADI_health_2017.csv
    ADI_2018/... (and so on through 2021)
    price_paid/
        pp-2017-part1.csv
        pp-2017-part2.csv
        pp-2018.csv  ...
    postcode/
        ONSPD_FEB_2024_UK.csv
    <force>/                                ← per-force monthly crime CSVs
        2020-01-<force>-street.csv
        2020-02-<force>-street.csv
        ...

/Volumes/crime_data/bronze/_meta/          ← Auto Loader writes here automatically
    _schemas/<table>/
    _checkpoints/<table>/
```

---

## Bronze Tables

| Table | Source glob | Key design note |
|---|---|---|
| `bronze_adi_claimant` | `**/ADI_claimant*.csv` | `year` derived from `ADI_<YYYY>` folder name |
| `bronze_adi_crime` | `**/ADI_crime*.csv` | All `*_rate` columns preserved |
| `bronze_adi_health` | `**/ADI_health*.csv` | All `*_prevalence_rate` columns preserved |
| `bronze_price_paid` | `price_paid/pp-*.csv` | Headerless — explicit 16-col schema supplied |
| `bronze_postcode_lookup` | `postcode/ONSPD_*.csv` | Only `pcds` + `lsoa11` materialised (of 51 cols) |
| `bronze_<force>_crime` | `**/*<force>-street*.csv` | One table per force in `POLICE_FORCES`; column headers snake-cased; forces with no CSVs are skipped |

All tables share two provenance columns:

| Column | Type | Meaning |
|---|---|---|
| `_source_file` | string | Full Volume path of the source CSV |
| `_ingest_ts` | timestamp | UTC timestamp when the row was landed |

---

## Auto Loader Key Options

| Option | Value | Reason |
|---|---|---|
| `cloudFiles.format` | `csv` | Source format |
| `cloudFiles.schemaLocation` | `_meta/_schemas/<table>` | Persists inferred schema across runs |
| `checkpointLocation` | `_meta/_checkpoints/<table>` | Tracks which files are already ingested |
| `trigger(availableNow=True)` | — | Processes all pending files then stops (batch-like) |
| `recursiveFileLookup` | `true` | Discovers CSVs inside year sub-folders |
| `header` | `false` (price-paid only) | PPD files have no header row |

---

## Running the Notebook

1. Upload `data/bronze/` to the Volume, preserving folder names (the `ADI_<YYYY>/` pattern
   is required for year derivation; per-force `<force>/` subfolders keep crime CSVs
   discoverable by the per-force glob).
2. Run `notebooks/01_ingestion_layer.ipynb` top-to-bottom on a Databricks cluster with UC
   enabled.
3. Section 7 prints a per-file row count for each materialised bronze table and asserts
   that every row has non-null provenance columns.
4. Re-running is safe — Auto Loader's checkpoint ensures already-ingested files are skipped.
5. Forces listed in `POLICE_FORCES` (section 1) whose CSVs aren't in the Volume yet are
   skipped at ingest time (`autoload_to_delta` returns early on no glob matches) and
   reported as `SKIPPED` by the validation cell. To onboard a new force, append it to
   `POLICE_FORCES`, upload its CSVs under `landing/<force>/`, and re-run.

---

## Downstream

The cleaning & validation layer (`notebooks/cleaning_and_validation_layer.ipynb`) reads
from these bronze tables via `spark.read.table("crime_data.bronze.<table>").toPandas()`
instead of reading CSVs directly with `glob.glob` / `pd.read_csv`.
