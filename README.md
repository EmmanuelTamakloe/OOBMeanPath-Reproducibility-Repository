# OOBMeanPath-Reproducibility-Repository
This repository contains the computational materials supporting the manuscript:
**OOBMeanPath: Out-of-Bag Path-Length Scoring and Finite-Forest Corrections for Isolation Forest**

OOBMeanPath modifies how fitted reference observations are scored in a subsampled Isolation Forest ensemble: for a reference observation, only trees whose construction subsamples excluded that observation are eligible for scoring. For a genuinely unseen query, every fitted tree is eligible, so OOBMeanPath reduces exactly to ordinary all-tree Isolation Forest scoring.

The repository is organized around three Jupyter notebooks. Each notebook corresponds to a distinct part of the empirical study described in the manuscript and Supplementary Material.

---

## Repository contents

```text
OOBMeanPath/
│
├── README.md
│
├── Code/
│   ├── OOBMeanPath_Real_Data_Analysis.ipynb
│   ├── OOBMeanPath_Ablation_Study.ipynb
│   └── OOBMeanPath_Table1_Empirical_Support.ipynb
│
├── Results/
│   ├── Real_Data_Analysis/
│   ├── Ablation_Study/
│   └── Table1_Empirical_Support/
│
└── Data/
    └── [dataset provenance / manifest files, if provided]
```

The exact directory layout may be adapted, but the three notebook filenames above should be kept unchanged because they are referenced explicitly in the manuscript and Supplementary Material.

---

## 1. Real-data analysis

### Notebook

`Code/OOBMeanPath_Real_Data_Analysis.ipynb`

### Purpose

This notebook performs the real-data benchmark and repeated-seed analyses across the 30 benchmark tasks. It includes:

- OOBMeanPath;
- standard Isolation Forest;
- DIF;
- SLAD;
- ECOD;
- KNN;
- COPOD;
- HBOS;
- OCSVM;
- CBLOF;
- LODA;
- ABOD;
- LOF; and
- FeatureBagging.

The workflow computes ROC-AUC, average precision, average ranks, runtime summaries, Friedman tests, pairwise Wilcoxon comparisons, bootstrap confidence intervals, method-coverage audits, leave-one-task-out summaries, and repeated-seed OOBMeanPath-versus-Isolation-Forest comparisons.

### Principal outputs

The principal output files referenced in the Supplementary Material include:

```text
all_files_complete_14_method_results.csv
all_files_dataset_metadata.csv
all_files_mean_performance.csv
all_files_mean_ranks_combined.csv
all_files_friedman.csv
paired_vs_baselines.csv
bootstrap_ci.csv
matched_seed_results.csv
matched_seed_mean_tests.csv
loader_audit.csv
```

The notebook may also generate additional audit, checkpoint, coverage, robustness, and figure files.

By default, the notebook writes its outputs to:

```text
oobmeanpath_all_dataset_outputs/
```

---

## 2. Ablation study

### Notebook

`Code/OOBMeanPath_Ablation_Study.ipynb`

### Purpose

This notebook contains the mechanism and tree-count ablation analyses. It includes:

1. a same-fitted-forest experiment in which AllTree and OOBMeanPath use the same realized Isolation Forest and differ only in tree eligibility for fitted reference observations;
2. the primary mechanism analysis based on the finite-forest correction;
3. robustness and leave-one-task-out diagnostics;
4. a nested tree-count study with

\[
T \in \{100, 250, 500, 1000\};
\]

5. Monte Carlo standard-error scaling analyses; and
6. tree-count convergence checks for the controlled effects and mechanism diagnostics.

### Principal outputs

```text
seed_level_results.csv
dataset_summary.csv
primary_mechanism_test.csv
controlled_effect_summary.csv
secondary_diagnostics.csv
leave_one_dataset_out.csv
partial_rank_robustness.csv
t_sensitivity_seed_level_all_datasets.csv
mcse_scaling_all_datasets.csv
mcse_scaling_overall_summary.csv
t_convergence_and_mechanism_all_datasets.csv
```

The notebook also produces additional reproducibility and audit files, including nested-prefix checks and loader diagnostics.

By default, the notebook writes its outputs to:

```text
OOBMeanPath_Unified_Ablation_outputs/
```

---

## 3. Empirical support for Table 1

### Notebook

`Code/OOBMeanPath_Table1_Empirical_Support.ipynb`

### Purpose

This notebook implements the direct reference-versus-held-out experiment used to support the operational distinction summarized in Table 1 of the manuscript.

For each benchmark task, the workflow repeatedly forms stratified reference/held-out splits, fits one Isolation Forest to the reference portion, and then:

- compares AllTree and OOBMeanPath scoring for fitted reference observations;
- measures changes in reference-sample percentile ranks and top-fraction membership;
- relates the normalized finite-forest correction to reference-sample rank movement; and
- verifies the exact equality of OOB-equivalent and ordinary all-tree scoring for genuinely held-out observations.

The held-out equality check is an implementation verification of the finite-forest identity for unseen queries; it is not treated as a separate performance test.

### Principal outputs

```text
table1_regime_seed_level.csv
table1_regime_dataset_summary.csv
table1_regime_overall_summary.csv
```

Additional audit and diagnostic outputs may include:

```text
table1_regime_loader_audit.csv
table1_regime_errors.csv
table1_regime_observation_sample.csv
```

By default, the notebook writes its outputs to:

```text
table1_regime_outputs/
```

---

## Data

The empirical study uses 30 benchmark tasks drawn from publicly available anomaly-detection sources, including the DAMI benchmark collection, ODDS, and UCI sources.

This repository does **not** need to redistribute third-party benchmark datasets unless redistribution is explicitly permitted by the corresponding source or license. Users should obtain the datasets from their original sources and preserve the associated attribution and licensing terms.

The notebooks read `.arff` and `.mat` files. Before execution, set the `DATA_DIR` variable in the relevant notebook to the directory containing the benchmark files. The notebooks are written to discover the supported dataset files directly from that directory.

For full reproducibility, a dataset manifest is recommended. Such a manifest can record:

- manuscript task name;
- original filename;
- source collection;
- original source URL or citation;
- sample size;
- processed feature count;
- anomaly-label convention;
- subsample size used by the experiment; and
- a SHA-256 checksum of the exact local file used in the study.

Related benchmark formulations are treated as distinct tasks when they correspond to distinct source formulations. The Supplementary Material discusses family-level sensitivity analyses for related task formulations.

---

## Software requirements

The notebooks use Python and the scientific Python ecosystem. Core dependencies include:

```text
numpy
pandas
scipy
scikit-learn
matplotlib
pyod
h5py
```

The real-data benchmark also uses:

```text
torch
deepod
```

for the DIF and SLAD baselines.

The real-data notebook contains dedicated environment handling for these deep baselines using:

```text
Python 3.10
DeepOD 0.4.1
PyTorch 1.12.1
```

For exact reproducibility, users should retain the package versions recorded in the notebook/environment used for the final analysis.

---

## Running the analyses

The three notebooks are independent computational workflows. A typical reproduction sequence is:

1. obtain the benchmark datasets from their original sources;
2. place the `.arff` and `.mat` files in a local data directory;
3. open the desired notebook;
4. set `DATA_DIR` in the configuration section;
5. confirm the output directory;
6. run the notebook from top to bottom; and
7. compare the generated summaries with the corresponding files in `Results/`.

The notebooks include loader audits and coverage checks. When a dataset requires a derived or repaired local representation, the workflow is designed to place that derived file in an output location rather than overwrite the original source file.

Random seeds and repeated-seed schedules used in the analyses are defined inside the notebooks. The default master seed is 42 where applicable.

---

## Mapping from manuscript analyses to code

| Manuscript analysis | Notebook | Representative outputs |
|---|---|---|
| 14-method real-data benchmark | `OOBMeanPath_Real_Data_Analysis.ipynb` | `all_files_complete_14_method_results.csv`, `all_files_mean_performance.csv`, `all_files_mean_ranks_combined.csv` |
| OOBMeanPath vs. Isolation Forest repeated-seed analysis | `OOBMeanPath_Real_Data_Analysis.ipynb` | `matched_seed_results.csv`, `matched_seed_mean_tests.csv` |
| Same-fitted-forest mechanism study | `OOBMeanPath_Ablation_Study.ipynb` | `primary_mechanism_test.csv`, `controlled_effect_summary.csv` |
| Robustness and partial-rank diagnostics | `OOBMeanPath_Ablation_Study.ipynb` | `leave_one_dataset_out.csv`, `partial_rank_robustness.csv` |
| Tree-count and MCSE scaling study | `OOBMeanPath_Ablation_Study.ipynb` | `mcse_scaling_all_datasets.csv`, `mcse_scaling_overall_summary.csv`, `t_convergence_and_mechanism_all_datasets.csv` |
| Reference-versus-held-out experiment supporting Table 1 | `OOBMeanPath_Table1_Empirical_Support.ipynb` | `table1_regime_seed_level.csv`, `table1_regime_dataset_summary.csv`, `table1_regime_overall_summary.csv` |

The Supplementary Material provides additional descriptions of these files and the analyses they support.

---

## Interpretation of the empirical results

The repository is intended to reproduce the analyses reported in the manuscript. OOBMeanPath should not be interpreted as a detector that is theoretically guaranteed to improve ROC-AUC or average precision.

Its primary role is to enforce query--construction separation when scoring observations that belong to the fitted reference sample. For genuinely unseen observations, all fitted trees are eligible and the scoring rule is exactly the ordinary all-tree Isolation Forest score.

The broader benchmark is therefore used to assess procedure-level empirical behavior and competitiveness, while the same-fitted-forest and reference-versus-held-out experiments isolate the specific mechanism studied in the paper.

---

## Reproducibility notes

- The 30 benchmark entries are benchmark **tasks**; some are alternative formulations of related source problems.
- The same-fitted-forest ablation keeps the fitted forest fixed and changes only tree eligibility.
- The tree-count study uses nested prefixes of a larger forest within each task/seed configuration so that comparisons across \(T\) are controlled.
- The Table 1 held-out equality result is exact by the scoring definition for unseen queries.
- Loader and coverage audit files should be retained with the repository because they document which benchmark files were successfully included in each workflow.
- Intermediate debugging files are not necessary for reproducing the reported manuscript results unless they are explicitly referenced by the Supplementary Material.

---

## Citation

If you use this code or the accompanying computational results, please cite the associated manuscript:

**OOBMeanPath: Out-of-Bag Path-Length Scoring and Finite-Forest Corrections for Isolation Forest**

Full bibliographic information can be added here after publication.

---

## Supplementary Material

Formal proofs, complete task-level results, auxiliary analyses, and the detailed reproducibility map are provided in the Supplementary Material accompanying the manuscript.

---

## License and third-party materials

The code license should be specified in the repository `LICENSE` file.

Third-party datasets remain subject to their original licenses, terms of use, and attribution requirements. Inclusion of code or result files in this repository does not alter the licensing conditions of the original benchmark data.
