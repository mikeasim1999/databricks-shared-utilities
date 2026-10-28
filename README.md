# Databricks Shared Utilities

Reusable utility notebooks and queries used across Databricks projects. Called via `%run` from Databricks Repos so every project references a single source of truth instead of duplicating utility code.

## Usage

Clone this repo as a Git folder in Databricks under a `main/` workspace folder, then reference notebooks from other projects:

```python
%run /Workspace/Users/<your-email>/main/databricks-shared-utilities/functions/data_quality_checks
```

## Structure

```
databricks-shared-utilities/
├── functions/
│   ├── data_quality_checks.ipynb       # Metadata-driven DQ validation (CRITICAL/WARNING)
│   ├── ensure_column_exists.ipynb      # Adds missing columns to a Delta table
│   ├── ensure_logging_table_exists.ipynb # Creates etl_run_log table if missing
│   ├── ensure_table_schema.ipynb       # Validates and enforces table schema
│   ├── load_sql_queries.ipynb          # Loads .sql files from workspace paths
│   └── log_etl_run.ipynb               # Writes run metadata to etl_run_log
├── queries/
│   ├── create_or_replace_views.dbquery.ipynb
│   ├── delete_table.dbquery.ipynb
│   ├── drop_view.dbquery.ipynb
│   ├── insert_data_quality_checks.dbquery.ipynb
│   └── logging_errors.dbquery.ipynb
├── clean-up/
│   └── vacuum.ipynb                    # Delta Lake VACUUM with per-schema storage tracking
├── ruff.toml                           # Ruff linter config
└── README.md
```

## Functions

| Notebook | Purpose |
|----------|---------|
| `data_quality_checks` | Reads check definitions from a `data_quality_checks` table and evaluates them against a DataFrame. CRITICAL failures halt the pipeline; WARNING failures are logged |
| `ensure_logging_table_exists` | Creates the `etl_run_log` Delta table if it does not exist |
| `log_etl_run` | Writes a row to `etl_run_log` with run ID, table name, row counts, timestamps, and status |
| `load_sql_queries` | Reads `.sql` files from a workspace path, splits on a delimiter (`-- << INCREMENTAL >> --`), and returns the appropriate query based on load mode |
| `ensure_column_exists` | Checks for a column on a Delta table and adds it with a default if missing |
| `ensure_table_schema` | Validates that a DataFrame's schema matches the target table, handling type mismatches and missing columns |

## Clean-Up

| Notebook | Purpose |
|----------|---------|
| `vacuum` | Runs `VACUUM` on all managed and external Delta tables across configured schemas. Measures before/after directory size to report GB freed per schema. Designed to run as a scheduled Databricks Job with email notifications via `dbutils.notebook.exit()` |

## Projects Using This Repo

- [denver-vehicle-listings](https://github.com/mikeasim1999/denver-vehicle-listings) — Lakehouse pipeline for Denver metro vehicle listing data
