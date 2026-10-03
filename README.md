# Ship engine anomaly detection

An exploratory comparison of statistical screening and unsupervised anomaly detection for unusual engine sensor observations in a fictitious shipping business scenario.

[Read the executed analysis notebook](DiDonato_Alex_CAM_C101_W5_Mini-project.ipynb).

[Read the project report](reports/ship-engine-anomaly-detection-report.pdf) — A stakeholder-facing summary of the approach, findings, limitations and recommendations, updated to match the revised notebook.

## Academic context and data

Completed by Alex DiDonato for the Cambridge Data Science Career Accelerator. The course supplied the dataset and project brief; the analysis, code, visualisations and interpretation are my work. This repository presents the completed academic analysis for a data science portfolio.

The course CSV contains 19,535 observations and six numeric features: engine rpm, lubrication oil pressure, fuel pressure, coolant pressure, lubrication oil temperature and coolant temperature. Execution found no missing values or duplicate rows. It contains no verified fault labels.

- [Existing course data download](https://raw.githubusercontent.com/fourthrevlxd/cam_dsb/main/engine.csv), loaded directly by the notebook; internet access is required.
- Original reference supplied in the brief: Devabrat, M. (2022), *Predictive Maintenance on Ship's Main Engine using AI*, [DOI: 10.21227/g3za-v415](https://doi.org/10.21227/g3za-v415). The original notebook records access on 5 March 2024.


## Methods and verified results

The notebook preserves descriptive statistics, histograms, box plots and Q-Q plots, then explores:

- IQR screening, selecting rows with at least two flagged features.
- One-Class SVM on standardised features, selecting RBF gamma = 0.05 and nu = 0.03.
- Isolation Forest on unscaled features, selecting 200 trees, contamination = 0.03 and random_state = 42. Other tree counts remain in the experiments.

A clean-kernel execution on 3 October 2026 with Python 3.12.0 produced:

| Selected method | Candidate observations | Percentage |
| --- | ---: | ---: |
| IQR: at least two flagged features | 422 | 2.16% |
| One-Class SVM | 583 | 2.98% |
| Isolation Forest: 200 trees | 587 | 3.00% |

Exactly one IQR-flagged feature occurs in 4,214 rows (21.57%); at least one occurs in 4,636 rows (23.73%). Pairwise candidate overlaps are 158 for IQR/SVM, 237 for IQR/Isolation Forest and 334 for SVM/Isolation Forest. All three flag 144 observations. The two plotted PCA components explain 18.99% and 17.69% of standardised variance (36.69% combined, calculated before rounding).

The maximum coolant temperature (195.53) is suspicious and needs investigation. It is flagged by the SVM and the coolant IQR rule, but not by the selected row-level IQR rule or Isolation Forest. These decisions do not confirm or rule out a sensor fault.

## Interpretation and limitations

The 1–5% anomaly range is an **assignment assumption**, not measured fault prevalence. All flags are candidate anomalies requiring investigation. Pairwise matrices are agreement tables, not evaluations against ground truth; agreement does not prove true anomalies, accuracy or superiority.

This analysis fits, selects and inspects methods on the same dataset. Generalisation to new observations has not been evaluated. PCA supports visual exploration but loses most of the variance here and cannot establish detection accuracy or equivalent model performance. Runtime measurements are single-run observations on one machine. The preference for Isolation Forest is provisional, based on observed runtime and practical simplicity rather than proven accuracy.

Domain review, measurement provenance and evaluation on independent observations with verified outcomes are needed before operational use.

## How to run

Use Python 3.12.0 for the tested setup. Versions in `requirements.txt` are the direct analysis and notebook execution dependencies actually used, rather than a full environment freeze. 

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

Open the notebook in VS Code with the Jupyter extension, select `.venv` as the kernel, restart the kernel and run all cells. Retained outputs allow reading the analysis on GitHub without execution. The full parameter experiments include forests of up to 5,000 trees and may take several minutes.

For a clean programmatic execution from the repository root (this replaces saved outputs):

```powershell
.\.venv\Scripts\python.exe -c "import nbformat; from nbclient import NotebookClient; from jupyter_client import KernelManager; import sys; p='DiDonato_Alex_CAM_C101_W5_Mini-project.ipynb'; n=nbformat.read(p,as_version=4); km=KernelManager(kernel_name='python3'); km.kernel_spec.argv=[sys.executable,'-m','ipykernel_launcher','-f','{connection_file}']; NotebookClient(n,km=km,timeout=600).execute(); nbformat.write(n,p)"
```
