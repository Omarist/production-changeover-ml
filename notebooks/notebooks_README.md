# Notebooks

Run notebook 01 first. All paths are relative to this `notebooks/` folder; the project root is one level up (`../`). The other notebooks can then be run in any order.

| Notebook | Reads | Writes (locally, not committed) |
|---|---|---|
| `01_Data_Preparation_and_Cleaning_GitHub.ipynb` | `../data.csv` | `../cleaned_data.csv`, `../normalized_data.csv` |
| `02_Logistic_Regression_Baseline_GitHub.ipynb` | `../normalized_data.csv` | `../outputs/` (features, coefficients, predictions) |
| `03_Logistic_Regression_SGD_GitHub.ipynb` | `../normalized_data.csv` | `../outputs/lr_tuned_*.csv` |
| `03_1_Logistic_Regression_GridSearch_GitHub.ipynb` | `../normalized_data.csv`, `../data.csv` | none |
| `03_2_Logistic_Regression_GridSearch_Undersampled_GitHub.ipynb` | `../data.csv` | none |
| `04_Decision_Tree_Baseline_GitHub.ipynb` | `../normalized_data.csv` | none |
| `05_Decision_Tree_Optimized_GitHub.ipynb` | `../normalized_data.csv` | `../outputs/dt_*.csv` |
| `06_TimeSeries_RandomForest_GridSearch_GitHub.ipynb` | `../data.csv` | `../outputs/` (metrics, importances, model) |
| `06_TimeSeries_RF_FullData_GitHub.ipynb` | `../data.csv` | `../outputs/` (metrics, importances) |

## Data

The dataset is public (CC BY 4.0) but not included in this repository. Download `data_v1_0_1.csv` from https://doi.org/10.5281/zenodo.15735480 and save it as `data.csv` in the project root. The full citation is in the main README.

## Notes

- Install the packages first with `pip install -r requirements.txt` from the project root.
- Generated CSV files, the fitted model and the `outputs/` folder are ignored by `.gitignore`. Do not add them to the repository.
- `Label_03`: 1 = Production, 0 = Changeover.
- The two notebooks `06_*` run grid searches that can take several minutes.
