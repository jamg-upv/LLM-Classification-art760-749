# Changelog — ART-760

All notable changes to this subproject.
Versions follow the `art760/vX.Y.Z` tag convention described in the root README.


## [1.1.0] – 2026-07-31

### Added
- Module 5 (`module-05-stability`): iteration-sufficiency check. Replicates the
  full simulation with independent seeds and reports how far both ends of the
  95% CI move between replications, against a user-declared target precision.
- Estimated requirement `N_Required` per bound, derived from the 1/sqrt(N)
  scaling of percentile sampling error, plus a suggested iteration count with a
  20% margin — replaces trial and error when the target is not met.
- What-if parameter `N_ITER_CHECK`, defaulting to `N_ITER`, so alternative
  iteration counts can be explored without altering the main experiment.
- Provenance block recording corpus, parameters, base seed, seed-derivation
  rule and full seed vector, making the check reproducible from `SEED` alone.
- `module-05-save` chunk: writes the provenance block and stability table to a
  new optional `Stability` sheet.
- Automatic consistency check: replication 1 reuses `SEED` and its bounds are
  compared against `report_df`, so any divergence between the replication
  engine and the main pipeline surfaces as a warning instead of silently wrong
  numbers.
- Module 5 in the Python/Jupyter supplementary notebook, keeping both
  implementations at feature parity.
- Simulation distribution (`probs_k`) recorded in the provenance block, so a
  saved results file documents which scenario produced it.
- Worked examples: 200-item synthetic corpus under both scenarios, with the
  results of a complete run, plus a Scenario B run from each implementation for
  cross-comparison.

### Changed
- Modules 3 and 4 refactored to expose the pipeline internals as named
  functions — `.simulate_matrix()`, `.metrics_table()`, `.ci_lo()`, `.ci_hi()`
  — so Module 5 reuses them instead of restating simulation or metric logic.
  Results are unchanged: the random-number sequence and every metric definition
  are identical, and the consistency check verifies this on each run.
- Seeds for replications 2..K are now drawn from a stream initialised with
  `SEED` rather than taken as consecutive integers. Dispersed across the integer
  range, still fully recoverable from `SEED`.
- Notebook and example filenames normalised: single `ART760` prefix, no spaces.

### Fixed
- Excel documentation referred to a `Validation` sheet that the notebook never
  wrote. The known-case vectors are written to `Simulations`; the per-case
  results are saved to `Inspection` by `inspection-save`.
- Notebook documentation stated that `Spec` was read only by Module 1; it is
  read again by Module 2 and determines the simulation scenario.
- Notebook documentation stated that Module 3 skips simulation when
  `Simulations` already contains data; Module 3 always overwrites the sheet.

  
## [1.0.0] — 2026-07-29

First functional release.

- Monte Carlo simulation procedure for empirical estimation of
  performance baselines (recall, specificity, precision, NPV, F1-score, F2-score, Balanced Accuracy, and MCC) in imbalanced datasets.
- Notebook runs end to end from raw input to summary tables.
- Subproject README with run instructions and dependency list.
