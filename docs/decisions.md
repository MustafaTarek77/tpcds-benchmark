# Decision log

| Decision | Status | Reason / follow-up |
| --- | --- | --- |
| DuckDB as initial engine | Accepted | Preferred in `PLAN.md`; revisit if baseline feasibility exposes a blocker. |
| Python virtual environment | Implemented | Keep dependencies local to the project. |
| DuckDB 2.0.0 initial pin | Provisionally accepted | Installed and extension loaded locally; baseline compatibility still needs a reproduction run. |
| Local scripts and database files | Accepted design | Keep the initial workflow small and reproducible. |
| Separate baseline, experiment configs, raw results, and analysis | Accepted design | Keep provenance and evidence traceable. |
| Raw runs and datasets excluded from Git | Implemented ignore rules | Retain locally; version small summaries and report inputs. |
| Upstream repository and commit | Pending | Complete the candidate audit before selection. |
| Contribution scope | Pending | Choose one or two gaps after reproducing upstream. |
| Reference scale and smaller scales | Pending | Measure capacity and verify generator-supported scales. |
| Timing, cache, timeout, and repetition protocol | Pending | Finalize after baseline reproduction. |
