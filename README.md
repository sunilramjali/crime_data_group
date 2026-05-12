# UK Police Crime Data Pipeline

A portfolio data engineering project that ingests UK Police crime data for 4 forces, cleans and enriches it with population / house price / deprivation data, and produces a BI-ready dataset at the grain `lsoa × month × crime_category`.

**Stack (target):** PySpark → Databricks (Bronze / Silver / Gold) → Snowflake (star schema) → Power BI
**Stack (current, while learning):** local PySpark + parquet on disk, mirroring the Bronze / Silver / Gold layout

> Status: scaffolding stage — Databricks, Snowflake and Power BI folders are placeholders. See [docs/architecture.md](docs/architecture.md) for the target architecture and [docs/crimedata.md](docs/crimedata.md) for the full brief.

---

## Architecture (target)

```
Raw Police CSVs ─► Databricks/PySpark ETL ─► Snowflake warehouse ─► Power BI
                   (Bronze → Silver → Gold)   (fact + dims)
```

The same Bronze / Silver / Gold layering is used **locally on disk** under `data/` while learning, so the migration to Databricks is a path change, not a code change.

## Repository layout

link: https://github.com/sunilramjali/crime_data_group.git

| Folder | What's in it |
|---|---|
| `src/crime_pipeline/` | Reusable Python package — ingestion, cleaning, enrichment, aggregation, validation |
| `notebooks/` | Numbered Jupyter notebooks that import from `src/` and demo each layer |
| `config/` | YAML config (forces, date range, paths, reporting grain) |
| `data/` | Local Bronze / Silver / Gold parquet (gitignored, except a small `samples/` slice) |
| `docs/` | Brief, architecture diagram source, data dictionary, decision log |
| `databricks/` | Placeholder — Databricks notebooks will live here once we migrate |
| `snowflake/` | Placeholder — DDL, views, and load scripts for the warehouse |
| `powerbi/` | Placeholder — `.pbix` files and dashboard screenshots |

## Quickstart

```bash
# 1. Clone and enter the repo
git clone <repo-url>
cd crime_project

# 2. Create a virtual environment (Python 3.10+ recommended)
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Copy the env template and fill in any keys you have
copy .env.example .env        # Windows
# cp .env.example .env        # macOS / Linux

# 5. Run the exploration notebook
jupyter notebook notebooks/01_explore_raw.ipynb
```

## Data

Crime CSVs come from [data.police.uk](https://data.police.uk/data/) for the 4 forces listed in `config/pipeline_config.yaml`. Enrichment datasets (population, house price, deprivation) are publicly available — sources will be documented in `docs/data_dictionary.md` as they are added.

**The `data/` folder is gitignored.** A small sample slice lives in `data/samples/` so the notebooks are runnable on a fresh clone.

## Reporting grain & metrics

Final aggregated output sits at `lsoa_code × year_month × crime_category` with:
- `crime_count`
- `crime_rate_per_1k` (count ÷ population × 1000)
- derived month / year fields for trend analysis

## Engineering principles

- **Iterative ingestion** — never load all CSVs into memory at once
- **Separation of layers** — ingest, clean, enrich, transform, aggregate are separate modules
- **Validation between layers** — row-count, null, duplicate, and grain checks live in `src/crime_pipeline/validate.py`
- **Config over hardcoding** — forces, dates, paths come from `config/pipeline_config.yaml`

## Roadmap

- [x] Repo scaffolding
- [ ] Local Bronze ingest (1 force, 1 month)
- [ ] Local Silver clean + validation
- [ ] Enrichment joins
- [ ] Local Gold aggregation
- [ ] Migrate to Databricks
- [ ] Load Gold into Snowflake (star schema)
- [ ] Power BI dashboards

## License / use

Personal portfolio project. UK Police data is published under the [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/).