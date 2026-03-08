# Taxi Data Pipeline

Small data engineering project for loading NYC Yellow Taxi trip data into PostgreSQL.

## What This Project Does

- Starts a local PostgreSQL database (plus pgAdmin) with Docker Compose.
- Downloads monthly NYC Yellow Taxi CSV data from the DataTalksClub release bucket.
- Streams data in chunks with pandas and loads it into a target PostgreSQL table.
- Includes a minimal sample script that writes Parquet output (`pipeline/pipeline.py`).

## Repository Structure

```text
.
├── README.md
├── pipeline
│   ├── docker-compose.yaml
│   ├── Dockerfile
│   ├── ingest_data.py
│   ├── main.py
│   ├── pipeline.py
│   ├── pyproject.toml
│   └── uv.lock
└── test/
```

## Prerequisites

- Docker + Docker Compose
- Python 3.12+
- `uv` (recommended, because this repo includes `uv.lock`)

## Quick Start

### 1. Start PostgreSQL and pgAdmin

From the `pipeline/` directory:

```bash
docker compose up -d
```

Services from [`pipeline/docker-compose.yaml`](/home/it_weirdtdd/projects/Taxi-001/pipeline/docker-compose.yaml):

- PostgreSQL: `localhost:5432`
  - DB: `ny_taxi`
  - User: `root`
  - Password: `root`
- pgAdmin: `http://localhost:8085`
  - Email: `admin@admin.com`
  - Password: `root`

### 2. Install Python dependencies

From the `pipeline/` directory:

```bash
uv sync --locked
```

### 3. Run the ingestion script

```bash
uv run python ingest_data.py --year 2021 --month 1 --target-table yellow_taxi_data
```

Useful flags:

- `--pg-user` (default: `root`)
- `--pg-pass` (default: `root`)
- `--pg-host` (default: `localhost`)
- `--pg-port` (default: `5432`)
- `--pg-db` (default: `ny_taxi`)
- `--year` (default: `2021`)
- `--month` (default: `1`)
- `--target-table` (default: `yellow_taxi_data`)
- `--chunksize` (default: `100000`)

## Running with Docker Image

From the `pipeline/` directory:

```bash
docker build -t taxi-ingest:latest .
```

Then run the container against the compose database:

```bash
docker run --rm --network host taxi-ingest:latest --pg-host localhost --year 2021 --month 1
```

## Notes

- `ingest_data.py` creates/replaces the target table on the first chunk, then appends the rest.
- If the table already exists and you want to keep old data, use a different `--target-table`.
- The `test/` directory is currently empty.
