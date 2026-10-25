# Notebooks

Run the notebooks in order. All paths are relative to this `notebooks/` folder; the project root is one level up (`../`).

| Order | Notebook | Reads | Writes (locally, not committed) |
|---|---|---|---|
| 1 | `01_Data_Preparation_and_Cleaning_GitHub.ipynb` | `../data.csv` | `../cleaned_data.csv`, `../normalized_data.csv` |
| 2 | `02_Logistic_Regression_Baseline_GitHub.ipynb` | `../normalized_data.csv` | `../outputs/` (features used, coefficients, test predictions) |

## Data

The dataset is public (CC BY 4.0) but not included in this repository. Download `data_v1_0_1.csv` from https://doi.org/10.5281/zenodo.15735480 and save it as `data.csv` in the project root. The full citation is in the main README.

## Notes

- Install the packages first with `pip install -r requirements.txt` from the project root.
- Generated CSV files and the `outputs/` folder are ignored by `.gitignore`. Do not add them to the repository.
- `Label_03`: 1 = Production, 0 = Changeover.
