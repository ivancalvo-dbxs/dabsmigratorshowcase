# bike_sharing_bundle

Databricks Asset Bundle (DABs) project.

## Layout

- `databricks.yml` — bundle entrypoint and target definitions.
- `resources/` — one YAML per Databricks resource (jobs, pipelines, alerts, sql_warehouses).
- `src/` — source code grouped by the asset that owns it.
- `tests/` — pytest unit tests.
- `.github/workflows/` — CI/CD pipelines for GitHub Actions.

## Resources

| Resource | Type | Description |
|---|---|---|
| `dbdemos_job_bike_init_ivan_calvo` | Job | Initializes bike data and triggers the SDP pipeline |
| `dbdemos_pipeline_bike_icalvo_hackaton_ws_catalog_ivandb2` | Pipeline | Medallion architecture (bronze/silver/gold) for bike sharing data |
| `jobs_and_pipelines_failed` | Alert | Monitors for job/pipeline failures |
| `serverless_starter_warehouse` | SQL Warehouse | Serverless Pro warehouse for SQL queries |

## Local development

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
databricks bundle validate
```

## Deploying

Production deploys run through CI only — never `databricks bundle deploy -t prod` from a laptop.

| Target | Trigger |
|---|---|
| `dev` | `databricks bundle deploy -t dev` from your machine |
| `staging` | merge to `main` (CI) |
| `prod` | tag/release with manual approval (CI) |

## CI/CD secrets

Configure these in your GitHub repository's secret store (never commit them):

- `DATABRICKS_HOST` — workspace URL (store as an environment-scoped secret so staging and prod each supply their own value)
- `DATABRICKS_CLIENT_ID` — service principal application (client) ID
- `DATABRICKS_CLIENT_SECRET` — service principal client secret
- `catalog` — Unity Catalog catalog name (mapped to `BUNDLE_VAR_catalog` in the pipeline)
- `schema` — Unity Catalog schema name (mapped to `BUNDLE_VAR_schema` in the pipeline)

`DATABRICKS_BUNDLE_ENV` is hardcoded per pipeline step (`staging` or `prod`) and tells the CLI which target to deploy to.
