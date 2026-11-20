# Baseline assessment

Status: preliminary reconnaissance only; no upstream has been selected or executed.

GitHub API metadata was inspected on 2026-11-19:

| Candidate | Observed HEAD | License observation | Follow-up |
| --- | --- | --- | --- |
| [GEizaguirre/duckdb-tpc](https://github.com/GEizaguirre/duckdb-tpc) | `05e60cce47d4f0b25b4e8618e95a12e022ef17e9` | API returned no recognized license | Inspect scripts, licensing, external generator provenance, and compatibility. |
| [BenchBox-dev/benchbox](https://github.com/BenchBox-dev/benchbox) | `3e3d9f1d861524ecd4d81b666d94c509772d9987` | API identifies MIT | Inspect TPC-DS tooling and bundled tool licenses; determine scope and existing features. |

BenchBox's inspected Python metadata accepts Python 3.12 and declares a DuckDB
extra with `duckdb>=1.5.0,<3.0.0`. This is a dependency declaration, not evidence
that its benchmark has been tested in our environment.

Before selection, inspect each pinned candidate's README, license notices, generator
version, query coverage, parameterization, maintenance/concurrency capabilities,
setup requirements, and actual scripts. Record setup evidence and failures.
Then reproduce a small workload and identify gaps against the assignment.

The official DuckDB extension was installed and loaded locally, and
`tpcds_queries()` returned 99 templates. Availability does not demonstrate execution
coverage or official TPC-DS compliance. The extension is infrastructure, not the
selected independent GitHub baseline.
