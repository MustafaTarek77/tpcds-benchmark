# TPC-DS benchmark reproduction

Personal master's project for INFO-H419 Data Warehouses, Part I: reproduce a
local TPC-DS workload with DuckDB, measure performance at four dataset scales,
and document a verifiable contribution over a pinned public implementation.

## Current status

The project structure and environment are ready. DuckDB 2.0.0 and its `tpcds`
extension have been installed and checked locally; 99 query templates are
available. No benchmark dataset has been generated and no benchmark query
coverage has been demonstrated. The upstream baseline is not selected yet.

This is an educational reproduction. It does not claim official TPC-DS compliance.

## Setup

Prerequisites: Python 3.12 for the initial environment, its virtual environment
support, Git, and internet access for package and extension installation.

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Verify the engine and download/load the official extension without generating data:

```bash
python - <<'PY'
import duckdb

with duckdb.connect() as con:
    con.execute("INSTALL tpcds")
    con.execute("LOAD tpcds")
    print("DuckDB:", duckdb.__version__)
    print("Available templates:", con.execute("SELECT count(*) FROM tpcds_queries()").fetchone()[0])
PY
```

DuckDB's extension supplies engine functionality; it does not replace the
assignment's requirement to start from a selected GitHub implementation.

## Repository layout

```text
.
├── PLAN.md                     # Scope, phases, and success criteria
├── README.md                   # Entry point and setup
├── requirements.txt            # Pinned Python dependencies
├── docs/                       # Architecture, decisions, research, methodology
├── baseline/                   # Upstream provenance and checkout guidance
├── scripts/                    # Future local orchestration and analysis
├── experiments/                # Versioned experiment configurations
├── data/                       # Local generated databases and scratch data
├── results/
│   ├── raw/                    # Local detailed run records
│   └── summaries/              # Small versioned analysis outputs
└── report/                     # Report sources and selected figures
```

Start with [the architecture and flow](docs/architecture.md), then consult
[the implementation plan](PLAN.md) and [the decision log](docs/decisions.md).
Directory README files define what belongs in each location. The structure
will adapt to the selected upstream rather than duplicate its implementation.

## Next milestone

Audit at least two DuckDB-compatible implementations, select and pin one,
then reproduce its smallest practical supported workload with documented
commands and saved outcomes. Benchmark scripts and experiment configurations
will follow that audit.
