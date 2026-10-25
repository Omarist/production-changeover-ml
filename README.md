# production-changeover-ml

**Recognition of Changeover Activities in Production Machines Using Machine Learning**

Can a machine-learning model tell **production** from **changeover** (machine set-up) phases using only the machine's own control data, without any manual input from the operator? This repository contains the code of a project on a five-axis CNC milling machine that investigates exactly this question. It grew out of a bachelor's thesis at the Technical University of Applied Sciences Würzburg-Schweinfurt (THWS), degree programme Business and Engineering, and follows the CRISP-DM process.

## Why this matters

Changeover times are usually documented by hand in ERP systems, which makes the records variable and unreliable. Unreliable times weaken production planning, scheduling and lean-production efforts. Recognizing changeovers automatically from machine data could replace this manual documentation.

## Dataset

The project uses the public dataset *Production Data Set for Five-Axis CNC Milling with Multiple Changeovers*:

- Recorded in a laboratory at THWS on a **Spinner U5-620** five-axis milling machine with a **Siemens 840D-SL** control.
- Three different products, 30 manufacturing sessions, five complete changeover matrices.
- 170 features from the numerical control and the PLC, and 10 label sets.
- **Target:** `Label_03`, with **1 = Production** and **0 = Changeover**.
- Licence: **CC BY 4.0** (attribution required).

The data is **not stored in this repository** (the file is large and belongs to its authors). Download it yourself:

1. Open https://doi.org/10.5281/zenodo.15735480
2. Download the file `data_v1_0_1.csv` (about 79 MB).
3. Save it as `data.csv` in the project root, next to this README.

**Please cite the dataset:** Martinez, M., Schmitt, A.-M., Schiffler, A. & Engelmann, B. (2025). Production Data Set for five-Axis CNC Milling with multiple Changeovers. *Scientific Data* 12, 1067. https://doi.org/10.1038/s41597-025-05294-0

## Approach

1. **Preparation** (notebook 01): remove the other label sets (some combine `Label_03` with further information and would leak the answer), convert Boolean columns to integers, remove non-numeric administrative columns, fill missing values, min-max-scale the features, and rank features by their Pearson correlation with `Label_03`.
2. **Feature selection:** the 20 most negatively and the 20 most positively correlated features (40 in total).
3. **Modelling:** the thesis compares logistic regression (baseline, SGD, grid search, undersampled), decision trees (Gini and entropy, different depths) and a time-series random forest with grid search and time-aware cross-validation. Evaluation uses AUC, accuracy, precision, recall and F1-score with confusion matrices.

## What is in this repository

| Step | Notebook | Content |
|---|---|---|
| 1 | `01_Data_Preparation_and_Cleaning_GitHub.ipynb` | Cleaning, scaling, correlation analysis |
| 2 | `02_Logistic_Regression_Baseline_GitHub.ipynb` | Logistic-regression baseline on 40 features |

Notebooks for the decision-tree and random-forest experiments are not yet part of this repository and may be added later.

```text
production-changeover-ml/
├── README.md
├── requirements.txt
├── .gitignore
└── notebooks/
    ├── README.md
    ├── 01_Data_Preparation_and_Cleaning_GitHub.ipynb
    └── 02_Logistic_Regression_Baseline_GitHub.ipynb
```

## Results reported in the thesis

| Model | F1-score (Production = positive class) | Note |
|---|---|---|
| Logistic regression baseline | 0.867 | Notebook 02 in this repository |
| Decision tree (unrestricted depth) | 0.992 | Random split; notebook not yet included |
| Time-series random forest | 1.000 | Chronological splits; notebook not yet included |

Please read these numbers with care. The data comes from **one laboratory machine** and three products, so the results say little about other machines. Scores close to 1.00 are unusual for real production data, and the thesis itself calls a perfect score unrealistic for a general production environment. Also, a random train/test split on time-series data can be optimistic, because neighbouring rows are almost identical. Validation on other machines and with stricter splits is the natural next step.

## Getting started

1. Install a recent Python 3 version (the author used Python 3.12).
2. Download or clone this repository.
3. Open a terminal in the project folder, create a virtual environment and install the packages:

   ```bash
   python -m venv .venv
   # Windows:  .venv\Scripts\activate
   # macOS/Linux:  source .venv/bin/activate
   pip install -r requirements.txt
   ```

4. Download the dataset and save it as `data.csv` in the project root (see *Dataset* above).
5. Start Jupyter with `jupyter lab`, open the `notebooks/` folder and run notebook 01, then notebook 02.

Files created by the notebooks (`cleaned_data.csv`, `normalized_data.csv`, `outputs/`) stay on your computer. They are listed in `.gitignore` and are not committed.

## Methodological notes

- **Preprocessing in the model.** Imputation and standardization inside the model pipeline are fitted on training data only. The min-max-scaled file from notebook 01 is meant for exploration.
- **Feature selection.** The correlation ranking is computed on the whole dataset before splitting, so the test scores are slightly optimistic. A stricter design selects features on the training data only.
- **Train/test split.** Notebook 02 uses a stratified random split. A chronological or group-wise split is a stricter test.
- **Interpretation.** Correlations and model coefficients describe patterns in the data. They do not prove causes.

## Technologies

Python, pandas, NumPy, scikit-learn, matplotlib, seaborn, Jupyter.
