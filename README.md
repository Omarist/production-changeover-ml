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
2. **Feature selection:** the 20 most negatively and the 20 most positively correlated features (40 in total), used by notebooks 02 to 05.
3. **Modelling:** logistic regression (baseline, tuned, SGD, group-wise, undersampled), decision trees (Gini/entropy, different depths) and a time-series random forest with chronological validation.
4. **Evaluation:** AUC, accuracy, precision, recall and F1-score (Production = positive class) with confusion matrices.

## Notebooks

Run notebook 01 first. It creates `normalized_data.csv`, which notebooks 02 to 05 read. Notebooks 03_2, 06 and 06 (full) read the original `data.csv` directly.

| Notebook | Content | Reads |
|---|---|---|
| `01_Data_Preparation_and_Cleaning_GitHub` | Cleaning, scaling, correlation analysis | `data.csv` |
| `02_Logistic_Regression_Baseline_GitHub` | Logistic-regression baseline, 40 features | `normalized_data.csv` |
| `03_Logistic_Regression_SGD_GitHub` | Tuned logistic regression (GridSearchCV) and SGD variant | `normalized_data.csv` |
| `03_1_Logistic_Regression_GridSearch_GitHub` | Tuned logistic regression with group-wise validation (whole NC programs held out) | `normalized_data.csv`, `data.csv` |
| `03_2_Logistic_Regression_GridSearch_Undersampled_GitHub` | Logistic regression on a class-balanced training set | `data.csv` |
| `04_Decision_Tree_Baseline_GitHub` | Decision trees with Gini and entropy, impurity analysis | `normalized_data.csv` |
| `05_Decision_Tree_Optimized_GitHub` | Six tree variants (depth, leaf size, criterion) | `normalized_data.csv` |
| `06_TimeSeries_RandomForest_GridSearch_GitHub` | Random forest, chronological split, time-series cross-validation | `data.csv` |
| `06_TimeSeries_RF_FullData_GitHub` | Variant of notebook 06 without scaling and without the piece counter | `data.csv` |

## Results

Test-set scores from the original runs (Production = positive class). Notebooks 02 to 05 use a stratified random 80/20 split; notebook 03_2 uses the same kind of split on the raw signals.

| Model | Notebook | AUC | F1 | Note |
|---|---|---|---|---|
| Logistic regression baseline | 02 | 0.968 | 0.867 | 40 features |
| Logistic regression, tuned | 03 | 0.968 | 0.867 | L1 penalty, `C = 1` |
| Logistic regression, SGD | 03 | 0.956 | 0.848 | |
| Logistic regression, undersampled | 03_2 | 0.924 | 0.775 | 84 raw signals, balanced training set |
| Decision tree (Gini / entropy) | 04 | 0.994 | 0.991 / 0.992 | unrestricted depth |
| Decision tree, depth 5 | 05 | 0.954 to 0.969 | 0.861 to 0.867 | |
| Decision tree, depth 3 | 05 | 0.903 to 0.907 | 0.739 to 0.750 | |

**Please read these numbers with care.**

- A random train/test split on time-series data can be optimistic, because neighbouring rows are almost identical. The unrestricted trees fit the training data perfectly.
- The data comes from **one laboratory machine** and three products, so the scores say little about other machines.
- **Results of the group-wise logistic regression (03_1) and of both random-forest notebooks (06) are pending.** Earlier versions of these three notebooks accidentally kept the target column among the features, which gives meaningless perfect scores. This is fixed and now guarded by an assertion; the numbers will be added after re-running the corrected notebooks.

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
5. Start Jupyter with `jupyter lab`, open the `notebooks/` folder and run notebook 01, then the others. The grid searches in notebooks 06 can take several minutes.

Files created by the notebooks (`cleaned_data.csv`, `normalized_data.csv`, `outputs/`) stay on your computer. They are listed in `.gitignore` and are not committed.

## Methodological notes

- **No leakage in model pipelines.** Imputation and standardization inside the model pipelines are fitted on training data only. The min-max-scaled file from notebook 01 is meant for exploration.
- **Feature selection.** The correlation ranking is computed on the whole dataset before splitting, so the scores of notebooks 02 to 05 are slightly optimistic. A stricter design selects features on the training data only.
- **Model selection on the test set.** Notebook 05 ranks its variants by their test scores, and notebook 03 picks its illustrative F1 threshold on the test set. Both are optimistic.
- **Label definition.** `Label_03` is derived from the machine's own process state, which the NC and PLC signals describe directly. Very high scores can therefore partly reflect how the label is defined; the random-forest notebooks print the feature importances so this can be checked.
- **Interpretation.** Correlations and model coefficients describe patterns in the data. They do not prove causes.

## Technologies

Python, pandas, NumPy, scikit-learn, matplotlib, seaborn, Jupyter.
