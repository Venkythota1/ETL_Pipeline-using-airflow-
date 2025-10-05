# Amazon Books ETL (Airflow DAG)

A small Airflow ETL that scrapes Amazon search results for "data engineering" books, cleans/deduplicates the results, and inserts them into a Postgres `books` table.

This repository contains DAG code (under `dags/`), a `docker-compose.yaml` for local development, and minimal helpers to run the project locally.

## Repository layout

- `dags/` — Airflow DAGs and helpers (your `app.py` lives here)
- `docker-compose.yaml` — local development stack (if present)
- `config/` — Airflow configuration
- `logs/` — Airflow task logs (ignored)

## Quick setup (local)

These steps assume Windows PowerShell (you can adapt them for other shells).

1. Create a Python virtual environment and install deps

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
```

2. If you plan to run Airflow locally via `docker-compose` use the provided `docker-compose.yaml`. Make sure Docker Desktop is running.

3. Airflow connections and variables

- The DAG expects a Postgres connection. In Airflow UI create a connection (Admin → Connections) with:
  - Conn Id: `Amazon_books_connection` (or `books_connection`, but keep it consistent with `dags/app.py`)
  - Conn Type: Postgres
  - Host / Schema / Login / Password / Port as appropriate.

4. Adjust connection IDs in `dags/app.py` if you prefer a different name (search for `conn_id` and `postgres_conn_id`).

## Run the DAG (manual)

- If using Airflow via Docker Compose: start the stack, open the Airflow UI, unpause the DAG `fetch_and_store_amazon_books`, and trigger a manual run.
- If running Airflow locally (system install), ensure the DAGs folder is configured in `airflow.cfg` and restart the scheduler.

## Notes about scraping and production concerns

- Scraping Amazon is fragile: HTML structure, selectors and anti-bot measures can change and break the DAG.
- Consider using an API (Product Advertising API) or a scraping service for production.
- Avoid storing large payloads in XCom; for larger datasets write results to a staging table or object storage and pass only a pointer via XCom.
- Use idempotent inserts (Postgres `ON CONFLICT DO NOTHING`) or unique constraints to avoid duplicate rows on re-runs.

## Git and GitHub — create the repo and push

If you don't yet have a GitHub repository, you can create one via the website or `gh` CLI.

Web: create a new repository on GitHub and copy the remote URL.

Using Git and PowerShell locally (replace `<REMOTE_URL>`):

```powershell
cd C:\Users\venthota\WORKSPACE
git init
git add .
git commit -m "chore: initial commit - add Airflow DAG and project files"
git branch -M main
git remote add origin <REMOTE_URL>
git push -u origin main
```

Using GitHub CLI (if installed):

```powershell
gh repo create my-amazon-books-etl --public --source=. --remote=origin --push
```

## Recommended next steps (small checklist)

- [ ] Ensure Airflow connection id(s) in `dags/app.py` match the connection configured in Airflow.
- [ ] Replace prints with proper logging (`logging` module).
- [ ] Make DB inserts idempotent with `ON CONFLICT` or add a unique constraint.
- [ ] Move large payloads out of XCom and into object storage / staging table.
- [ ] Add CI (optional): linting, unit tests, and basic syntax checks for DAGs.

## Troubleshooting

- If the DAG doesn't appear in Airflow UI: ensure Airflow's `dags_folder` points to this repository's `dags/` path and the scheduler is running.
- If DB inserts fail: verify the connection credentials and that the `books` table exists (the DAG includes a create table task).

## License

This repository is provided under the MIT License — see `LICENSE`.
