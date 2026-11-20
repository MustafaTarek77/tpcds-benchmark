# Measurement methodology

Status: draft requirements for the future runner; no experiment results exist yet.

- Select a feasible reference scale using measured generation, storage, memory,
  spill, and execution constraints. Choose the three smaller supported scales near
  0.1, 0.2, and 0.5 times the reference and document deviations.
- Hold engine version, query set, thread count, memory limit, concurrency, timeout,
  query ordering, and cache/warmup policy consistent across comparable runs.
- Record source and upstream commits, machine metadata, generator provenance,
  requested scale, row counts, database size, and generation/loading duration.
- Time each query through complete result retrieval. Separate profiling overhead
  from ordinary measurements and generation/loading time from query execution.
- Record query ID, parameters/seed, trial, status, duration, and failure reason.
  Treat timeout and failure counts explicitly when aggregating results.
- Decide the repeat count, warmup/cache policy, timeout enforcement, and validation
  checks after the baseline audit, before collecting reportable measurements.
- Preserve raw records and derive summaries and figures reproducibly. Report
  limitations and compare only equivalent workloads and conditions.

See [the flow](architecture.md) for artifact locations and lifecycle.
