# TPC-DS Benchmark Reproduction — Implementation Plan

**Project:** INFO-H419 Data Warehouses — Part I (TPC-DS only)  
**Author:** Mohamed Samir  
**Status:** Planning / baseline selection  
**Preferred database:** DuckDB (selected for initial implementation; revisit only if the baseline audit exposes a blocker)

## 1. Objective and scope

Reproduce a local TPC-DS decision-support benchmark on a database platform, starting from an existing public implementation rather than building the entire benchmark from scratch. The project must be reproducible on ordinary computing infrastructure, evaluate the influence of data scale on performance, and clearly document any modifications relative to the selected upstream implementation.

**Scope:** Part I, TPC-DS only. TPC-DI is explicitly out of scope.

### Sources of requirements

- **Course project page (supplied screenshot):** Choose benchmark tooling/database, estimate the largest feasible dataset scale on the local machine, and benchmark that reference scale plus three smaller scales approximately **0.1×, 0.2×, and 0.5×** the reference. Deliver a **PDF report** and **ZIP containing code and reproduction instructions**.
- **Supervisor's instructions (supplied conversation):** Read the official TPC-DS specification, select a suitable platform, start from an existing GitHub repository (partial implementation allowed), cite the upstream source, and assess the work based on the **delta versus that original repository**.
- **Official reference specification:** https://www.tpc.org/TPC_Documents_Current_Versions/pdf/TPC-DS_v4.0.0.pdf

## 2. Success criteria

The project is complete when:

1. A chosen upstream implementation is identified, pinned to a **specific commit**, and credited (including license and any adaptations).
2. A local environment can be set up from documented steps, with version-pinned DuckDB, its TPC-DS extension, and any separate upstream tooling where practical.
3. TPC-DS tables can be created and populated with generated synthetic data.
4. A clearly identified and documented query workload runs; every query's outcome is logged (success, failure, timeout, and reason). **Do not claim full TPC-DS compliance or 99-query coverage until demonstrated.**
5. Machine limits are investigated and a defensible maximum *feasible* reference scale is selected, with disk, RAM, time, and any other limiting constraints recorded.
6. Benchmark measurements are collected at the reference scale and approximately 0.1×, 0.2×, and 0.5× it. If generator restrictions prevent exact fractions, document the nearest feasible supported scales and deviations.
7. Results are repeatable and include query latencies, failures, sizes, generation/loading durations, and enough environment metadata for comparison.
8. Improvements over upstream are implemented **only after auditing existing functionality**, with a before/after feature comparison and reproducible evidence.
9. A final report explains methods, results, limitations, and upstream attribution; a ZIP includes code and reproduction instructions but excludes large datasets, credentials, and transient build artifacts.

## 3. Decisions and guardrails

- **DuckDB is the preferred platform** because its embedded, OLAP-oriented design reduces setup overhead and it offers a TPC-DS extension. Audit at least two DuckDB-compatible upstream repositories or implementations before selecting a pinned baseline. PostgreSQL is a fallback only if DuckDB cannot meet the assignment requirements.
- Assess DuckDB's official `tpcds` extension (`INSTALL tpcds; LOAD tpcds;`) alongside independent upstream repositories. Verify supported TPC-DS specification version, data generation behavior, query templates, query parameterization, and maintenance/concurrency coverage. The extension alone is not the required GitHub baseline or a novel contribution. Pin upstream and DuckDB versions.
- Begin with **SF1 or the smallest practical supported scale** as a smoke test. Do **not** assume the final reference scale is SF10 or SF20 until the hardware has been measured.
- Baseline correctness and reproducibility come before custom features or SQL tuning.
- Avoid Kubernetes, Airflow, a separate database server, or other infrastructure unless required. Use an embedded DuckDB database file and local scripts for initial implementation.
- Keep benchmark conditions consistent between dataset sizes: DuckDB version, settings (notably threads and memory limit), query selection, machine, concurrency, timeout, and cache policy. Record unavoidable differences.
- Distinguish an **educational partial reproduction** from an **official TPC-DS-compliant result**. Do not report official TPC-DS metrics or compliance unless every required condition is met.
- Never silently change official SQL semantics to make queries pass. Document dialect adaptations and whether result equivalence was checked.
- Do not fabricate work history or benchmark results. A plan is not evidence of completed work.

### DuckDB-specific implementation notes

- Prefer the **official DuckDB `tpcds` extension** for a first local smoke test, but do not mistake using it for a contribution. Inspect available functions and supported parameters on the pinned version before writing generation scripts.
- DuckDB is embedded: the benchmark can run with its CLI or Python API against a persistent `.duckdb` file. A running database server, credentials, and port configuration are not required.
- Ensure each scale has an **isolated database** or a repeatable reset procedure so measurements do not mix datasets. Record requested and observed scale, actual row counts where possible, and the database file size.
- Check that the four chosen scales are supported by the actual generator. **Do not assume fractional scale factors work.** Use supported values near 0.1×, 0.2×, 0.5×, 1× or document deviations and professor approval where appropriate.
- Pin DuckDB version, extension version/behavior where available, thread count, memory limit, temporary directory, and query settings. Use DuckDB profiling / `EXPLAIN ANALYZE` selectively; avoid treating profiling overhead as unmodified runtime.
- Distinguish **engine-provided functionality**, **upstream repository functionality**, and **our original changes**. Choose a contribution only after checking both the repository and extension.

## 4. System architecture (target)

```text
TPC-DS specification and upstream implementation
                    |
          Data / query generation
                    |
          DuckDB data warehouse
                    |
         Reproducible query runner
                    |
       Raw run logs + timings + metadata
                    |
        Analysis tables / charts / report
```

The upstream repository may already implement some or all of these modules. Preserve or reuse its working components instead of rebuilding them unnecessarily.

## 5. Work phases and checkpoints (priority order, no calendar deadlines)

### Phase 1 — Research and feasibility

- Review the TPC-DS specification: schema, generated datasets, query workload, execution methodology, and the distinction between a partial reproduction and official benchmark compliance.
- Investigate existing public implementations and database candidates.
- Record the baseline repository, commit hash, license, supported features, limitations, and likely contribution opportunities.
- Document the DuckDB decision and why it is suitable for the assignment; keep PostgreSQL as a contingency, not a parallel implementation.

**Evidence:** `docs/spec-notes.md`, `docs/baseline-assessment.md`, and an explicit decision log. Mark activities accurately as completed, in progress, or outstanding.

### Phase 2 — Baseline reproduction

- Set up the selected environment using the upstream implementation.
- Compile or install required tools, generate a small synthetic dataset, and load or generate the schema in DuckDB.
- Execute representative queries, then expand coverage as feasible.
- Capture logs, working query counts, failures, timings, and setup instructions.

**Exit criterion:** A second developer can repeat the small-scale setup and run at least a documented representative workload. If this is not yet met, prioritize it immediately rather than claiming the phase is complete.

### Phase 3 — Implementation and extensions

- Compare actual upstream functionality with the course requirements.
- Select **one or two meaningful and feasible gaps**: e.g., scale orchestration, query-level result tracking, structured output, validation, or robust repeat execution.
- Implement only genuinely missing capabilities; add tests where possible.
- Keep changes traceable and document each delta over the pinned baseline.

**Exit criterion:** One verifiable improvement with a reproducible comparison against the unmodified baseline.

### Phase 4 — Experiments and evaluation

- Determine the largest feasible reference scale for the machine within the available time and disk budget.
- Prepare and benchmark four scales: approximately **0.1×, 0.2×, 0.5×, and 1.0×** reference.
- Use the same query set across scales; record wall-clock query latency, success/failure, timeout, data size, and resource usage where measurable.
- Perform repeated trials if practical. Define and document cache policy, warmup, query ordering, concurrency, timeouts, and DuckDB settings (threads, memory limit, temporary directory).
- Analyze scaling trends and compare upstream versus extended implementation on comparable runs. Improvements to test automation/coverage need not imply faster SQL execution.

**Exit criterion:** Raw results and reproducible figures/tables exist for all attainable scales, with explicit limitations for omissions.

### Phase 5 — Final documentation and delivery

- Produce a concise PDF technical report: architecture, setup, upstream attribution, experiment design, machine specs, measured scales, plots, findings, contribution delta, and limitations.
- Create a clean ZIP with implementation source and instructions to reproduce on standard computing infrastructure.
- Confirm fresh-clone setup steps, remove secrets and generated large files, and check that citations/license notices are retained.

**Exit criterion:** PDF and ZIP are ready for submission.

## 6. Measurement protocol (draft; finalize after baseline audit)

For each experiment, capture:

- Machine: CPU model and cores, RAM, operating system, available storage and storage type.
- Software: database version, benchmark/generator version, upstream commit, configuration.
- Dataset: requested scale factor, actual row counts where practical, database size, generation and load time.
- Queries: query/template ID, parameters or seed if applicable, trial number, execution time, success/failure, error message, timeout.
- Conditions: concurrency, per-query timeout policy, statistics/profiling policy, cache/warmup policy, DuckDB memory and thread settings, and any query adaptations.

Store raw records in machine-readable format (CSV or JSON), then derive aggregates and charts. **Do not compare different query sets across scales as if they were equivalent.** Separate build/load time from query execution time. Avoid claiming measurements represent official TPC-DS throughput or power metrics without following the specification.

## 7. Proposed repository layout

Keep the layout light and adapt it to the upstream project rather than forcing a duplicate structure.

```text
tpcds-benchmark/
├── PLAN.md                      # This implementation plan
├── README.md                    # Purpose, status, quick-start
├── docs/
│   ├── requirements.md          # Course and supervisor requirements
│   ├── spec-notes.md            # Notes from official benchmark spec
│   ├── baseline-assessment.md   # Repository candidates, selection, gaps
│   ├── decisions.md             # Decisions, assumptions, open questions
│   └── methodology.md           # Reproduction and measurement protocol
├── baseline/                    # Upstream checkout/submodule, if appropriate
├── scripts/                     # Only scripts we need beyond upstream
├── experiments/                 # Configurations and run manifests
├── results/                     # Small summaries (not huge generated data)
└── report/                      # Report sources/figures
```

Add `.gitignore` to exclude generated TPC-DS data, database volumes, logs, local secrets, and bulky experiment artifacts. If the upstream implementation is a submodule, pin its commit and provide initialization instructions. If code is vendored, preserve copyright/license notices.

### Candidate investigation checklist (do not preselect an unverified repo)

For each candidate, verify that the repository exists and is usable; record URL, commit hash, license, last known compatibility, what it actually implements, and setup evidence. Test whether it uses DuckDB's own TPC-DS extension or provides distinct scripts for query orchestration, validation, reporting, or scale-factor experiments. Select a real upstream baseline as instructed by the supervisor; DuckDB itself is the engine and its official extension is infrastructure, **not evidence of a delta over the selected GitHub repository**.

## 8. Codex CLI: initial commit instructions

**Initial task is documentation and reconnaissance, not full implementation.**

1. Inspect the directory before changing anything. If an upstream project is already present, read its README, license, version requirements, and scripts first; do not overwrite it.
2. Create/retain `PLAN.md` as the source of truth, plus a minimal `README.md`, `.gitignore`, and the `docs/` notes files listed above.
3. Record requirements in `docs/requirements.md`; explicitly distinguish requirements from proposed implementation choices.
4. Compare at least two DuckDB-compatible TPC-DS implementations or repositories **if network access is available**. Record URLs, actual commit hashes, licensing, TPC-DS version, setup needs, and demonstrated features. If network access is unavailable, document the pending investigation rather than inventing findings.
5. Inspect the machine (without modifying system settings) and record CPU, RAM, OS, free disk space, installed DuckDB (CLI/Python), Python, and build tools. Never commit secrets or personal files.
6. Write an actionable next-step checklist to get a **small-scale baseline running**. Do not download/generate large datasets yet.
7. If this directory is not already a Git repository, initialize one. Stage only intended documentation/configuration files. **Make an initial commit only if requested by the user or the Codex session explicitly authorizes it**; otherwise present the proposed files and commit message for approval.

**Suggested initial commit message:** `docs: initialize DuckDB TPC-DS plan and baseline evaluation`

**Expected Codex response after reconnaissance:** files created/changed, machine capabilities, repository candidates and evidence, recommended baseline, current blockers, and the exact next commands for the first supported small-scale DuckDB smoke test.

## 9. Open questions

- What are this machine's CPU, RAM, available SSD space, OS, and available DuckDB/Python versions?
- Which DuckDB-compatible upstream repository should be pinned as the baseline, and what does DuckDB’s `tpcds` extension already provide?
- What is the actual query coverage and does the upstream implement data maintenance and concurrency tests?
- What size and experiment duration can fit within the available machine resources and time budget?
- What benchmark coverage is expected for the submission?
- Which improvements are demonstrably new compared with upstream?

**Immediate priority:** Choose and execute a working baseline first, then select a focused contribution and run the mandatory multi-scale experiments. Treat phases as a priority-ordered checklist rather than fixed-length stages; record actual progress without implying work was already completed.
