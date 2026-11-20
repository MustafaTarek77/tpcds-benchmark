# Requirements

Source: the course screenshot and supervisor conversation summarized in `PLAN.md`.
The original materials have not been independently checked in this repository.

## Assignment requirements

- Part I: TPC-DS; TPC-DI is outside the project scope.
- Start from a public GitHub implementation, credit it, and assess the delta.
- Review the official TPC-DS specification.
- Investigate the largest feasible local reference dataset and benchmark it plus
  approximately 0.1, 0.2, and 0.5 times that scale. Document generator restrictions.
- Deliver a PDF report and ZIP with source and reproduction instructions.

## Project implementation choices

- Prefer DuckDB and an embedded local workflow.
- Pin the upstream commit and software versions.
- Reproduce the baseline before choosing original contributions.
- Record every query outcome and keep raw evidence for repeatable analysis.
- Describe the result as an educational reproduction unless compliance is demonstrated.

These choices support the assignment; they are not additional course requirements.
