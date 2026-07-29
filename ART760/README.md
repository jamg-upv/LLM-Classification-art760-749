# ART-760: Does the classifier beat chance?
**Version:** 1.0.0 (`art760/v1.0.0`) · [Changelog](CHANGELOG.md)

**A Monte Carlo simulation procedure for the empirical estimation of performance baselines in imbalanced datasets**

> Article under development for journal **WPOM**

## Citation

> [To be completed upon publication]

---
## License

This repository is made available for reproducibility purposes in connection with the article submission to WPOM.

[AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.html) - Juan A. Marin-Garcia (UPV)

---

## Overview

When evaluating a classifier on an imbalanced dataset, standard metrics such as Recall, F1, or MCC can be misleading if interpreted without a reference baseline. A classifier that merely reflects the label distribution of the corpus may appear to perform well on some metrics by chance alone.

This repository implements a **Monte Carlo simulation procedure** that estimates the empirical distribution of eight classification metrics under random labelling, given the specific label distribution of a corpus. The resulting baseline allows researchers to ask: *does my classifier beat chance for this dataset?*

---

## Method summary

For a given labelled corpus, the procedure:

1. Simulates `N` random classification vectors using the label distribution observed in the corpus (or a uniform distribution as an alternative scenario)
2. Computes eight classification metrics for each simulated vector against the gold standard
3. Estimates the empirical distribution of each metric and derives a 95% confidence interval by the percentile method (2.5th–97.5th percentiles)

Any real classifier scoring above the upper bound of this interval can be considered to beat chance for that metric.

---

## Repository contents

```
ART-760/
├── ART-760 baseline class.qmd       # Main Quarto notebook — full pipeline
├── ART760_template.xlsx       # Blank Excel template to get started
└── README.md
```

---

## Pipeline structure

The pipeline is implemented as a Quarto notebook (`.qmd`) with one chunk per module. Modules are executed sequentially in RStudio. Objects are passed between chunks via the global environment.

| Module | Chunk label | Description |
|--------|-------------|-------------|
| 0 | `module-00-setup` | Package installation, global parameters, Excel file selection |
| 1 | `module-01-synth-corpus` | Synthetic corpus generation from Spec sheet — auto-skipped if Corpus already has data |
| 2 | `module-02-read-validate` | Corpus and Spec reading, label validation, binarisation |
| 3 | `module-03-simulate` | Monte Carlo simulation — overwrites Simulations sheet |
| 4 | `module-04-report` | Metric computation and empirical summary — writes to Reporting sheet |

### Additional chunks (not part of main pipeline)

| Chunk label | Description |
|-------------|-------------|
| `heterogeneous-classifiers` | Generates synthetic heterogeneous classifiers simulating human reviewers with different labelling biases |
| `validation-known-cases` | Builds four known-outcome vectors (all positive, all negative, perfect, random 50/50) to verify metric calculations |
| `inspection` | Displays confusion matrix and 8 metrics per classifier/iteration for the most recent Module 4 run |
| `inspection-save` | Writes the inspection table to the Excel file — run optionally after `inspection` |

---

## Excel file structure

The pipeline uses a single Excel file as both input and output container. One file per scenario.

| Sheet | Role | Written by |
|-------|------|------------|
| `Spec` | Corpus parameters: `n_items`, `Label`, `Proportion` | User |
| `Corpus` | Gold standard dataset: `ID`, `Label` (+ optional `Title`, `Abstract`) | Module 1 or user |
| `Simulations` | Monte Carlo simulation matrix: `ID` + one column per iteration | Module 3 |
| `Reporting` | Empirical baseline summary: mean, median, SD, CI95 per metric | Module 4 |
| `Inspection` | Per-classifier confusion matrix and metrics (optional) | `inspection-save` chunk |
| `Validation` | Known-case validation results (optional) | `validation-check` chunk |

The file can be used in two ways:
- **Synthetic mode**: start from a `Spec`-only file, run all modules in order
- **Real dataset mode**: populate `Corpus` manually, skip Module 1

---

## Metrics computed

| Metric | Formula |
|--------|---------|
| Recall (Sensitivity) | TP / (TP + FN) |
| Specificity | TN / (TN + FP) |
| Precision (PPV) | TP / (TP + FP) |
| NPV | TN / (TN + FN) |
| F1-score | 2 · Precision · Recall / (Precision + Recall) |
| F2-score | 5 · Precision · Recall / (4 · Precision + Recall) |
| Balanced Accuracy | (Recall + Specificity) / 2 |
| MCC | (TP·TN − FP·FN) / √((TP+FP)(TP+FN)(TN+FP)(TN+FN)) |

Degenerate cases (zero denominator) are recorded as `NA`. The 95% CI is computed by the empirical percentile method.

---

## Interpreting the results

The output of Module 4 is **not** a measure of classifier performance — it is the empirical baseline that a random classifier would achieve given the label distribution of the corpus.

Two values serve as internal sanity checks:
- **Balanced Accuracy** should converge to **0.50**
- **MCC** should converge to **0**

as the number of iterations increases, since a random classifier has no discriminative capacity. The 95% CI quantifies the range within which a random classifier would fall 95% of the time. Any real classifier scoring above the upper bound of a metric can be considered to beat chance for that metric.

---

## Requirements

- R ≥ 4.2
- RStudio (recommended — required for interactive file picker)
- Quarto

R packages (auto-installed by Module 0 if missing):

```r
openxlsx, dplyr, purrr, tibble, rstudioapi
```

---

## Quick start

1. Open `ART-760 baseline class.qmd` in RStudio
2. Set `EXCEL_PATH` and `EXCEL_FILE` in Module 0, or leave both `NULL` to use the file picker
3. Set `POS_LABELS` and `NEG_LABELS` to match your corpus label values
4. Run modules sequentially: **0 → 1 → 2 → 3 → 4**
5. Results are written to the `Reporting` sheet of the Excel file

To use a real dataset, populate the `Corpus` sheet manually and skip Module 1.

---

## Validation

Before applying the procedure to real data, run the `validation-known-cases` chunk followed by Module 4 and the `inspection` chunk. The four known-outcome vectors produce theoretically predictable metric values that confirm the pipeline is computing correctly:

| Case | Expected Recall | Expected Specificity | Expected MCC | Expected Bal. Acc. |
|------|----------------|---------------------|-------------|-------------------|
| `all_positive` | 1.0 | 0.0 | 0.0 | 0.5 |
| `all_negative` | 0.0 | 1.0 | 0.0 | 0.5 |
| `perfect` | 1.0 | 1.0 | 1.0 | 1.0 |
| `random_fair` | ≈0.5 | ≈0.5 | ≈0.0 | ≈0.5 |

---

## Reference corpus (Alfalla-Luque et al., 2023)

The procedure has been validated on a systematic review corpus of 593 references (70 positives, 523 negatives; prevalence = 11.8%), which represents a typical highly imbalanced screening scenario in operations management research.

---

## Supplementary material

A Python/Jupyter equivalent implementation is available as supplementary material. It provides identical functionality using `pandas`, `numpy`, and `openpyxl`.

---

