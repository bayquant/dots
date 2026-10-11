---
name: prefect
description: >
  Naming and structure conventions for Prefect 3 flows, tasks, flow runs,
  deployments, tags, work pools and prefect.yaml. Use this whenever the user
  is writing, reviewing, renaming or deploying a Prefect flow, task or
  deployment, editing prefect.yaml, or asking how to name or organize Prefect
  objects, even if they don't mention naming.
---

# Prefect Conventions

Prefect 3 has no official naming-conventions page. Only the deployments page
gives naming guidance (marked **docs** below). Everything marked **convention**
is this user's choice, not a Prefect requirement. The docs state no character
or length restrictions for any name.

## Rules

| Object | Rule | Example | Source |
|---|---|---|---|
| Flow function | `snake_case`, noun phrase naming the outcome | `daily_prices_ingest` | convention |
| Flow `name=` | Always set explicitly, `kebab-case`, same words as the function | `daily-prices-ingest` | convention |
| Flow file | `flows/<domain>/<flow>.py` | `flows/data/daily_prices_ingest.py` | convention |
| Task function | `snake_case`, verb first. Keep the default name; set `name=` only when needed | `fetch_prices`, `write_parquet` | convention |
| `flow_run_name` | Template using the parameters that identify the run | `"{ticker}-{run_date:%Y-%m-%d}"` | docs (mechanism) |
| Deployment | Describes how, where or when it runs. One pattern everywhere | `daily`, `weekly-backfill` | docs (pattern), convention (values) |
| Tags | lowercase, few and stable | `data`, `database`, `nautilus` | convention |
| Work pool | One per infrastructure type | `droplet-process` | convention |
| Version | Leave unset, so it is the first 8 characters of the commit hash | n/a | docs |

## Why

- **Set the flow `name=` explicitly.** It defaults to the function name, so a
  refactor would silently rename the flow and split its run history.
- **Flow names say what, deployment names say how, where or when** (docs).
  Deployments display as `flow-name/deployment-name`, so don't repeat the flow
  name inside the deployment name.
- **Avoid `prod`, `default`, `main` as deployment names** (docs). With one
  environment, an environment prefix adds nothing. If a second appears, pick one
  pattern such as `{variant}-{environment}` and apply it to every deployment.
- **Task identity.** A task's key is a hash of its name and the function's fully
  qualified name (docs), so renaming or moving a task function changes its
  identity.
- **Tags are inherited by flow runs** and drive tag-based concurrency limits
  (`@task(tags=["database"])`; the limit is stored as `tag:database`).

## prefect.yaml

- Each entry in `deployments` needs a unique `name`.
- If two flows would share a deployment name, prefix it with the flow:
  `flow-name/daily`.
- `entrypoint` paths are relative to the project root.
- Target one deployment with `--name`, several by repeating it, all with
  `--all`. CLI overrides are ignored when targeting more than one.

```yaml
deployments:
  - name: daily
    entrypoint: flows/data/daily_prices_ingest.py:daily_prices_ingest
    tags: [data]
    work_pool:
      name: droplet-process
```

## Setup in this user's projects

- The server runs from `deploy/prefect/` in the website repo, backed by the
  shared Postgres in `deploy/postgres/`. Workers on the droplet reach the API at
  `http://prefect:4200/api` over the `backend` Docker network, not the public URL.
- No worker is set up yet. Check `deploy/prefect/README.md` before assuming one.

## Sources

- https://docs.prefect.io/v3/concepts/deployments.md
- https://docs.prefect.io/v3/how-to-guides/workflows/custom-metadata.md
- https://docs.prefect.io/v3/how-to-guides/deployments/prefect-yaml.md
- https://docs.prefect.io/v3/how-to-guides/workflows/tag-based-concurrency-limits.md
