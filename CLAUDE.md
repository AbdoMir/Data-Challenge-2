# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Running the project

```bash
# Launch Jupyter notebooks
uv run --with notebook jupyter notebook --notebook-dir=notebook --port=8888 --no-browser

# Serve pipeline documentation
python -m http.server 8000 --directory docs
```

## Installing dependencies

```bash
uv sync
# or
uv pip install -r requirements.txt
```

## Architecture

The project is organized into four sequential phases, each in its own subfolder under `notebook/`:

| Folder | Phase | Description |
|--------|-------|-------------|
| `Projet0/` | EDA | Exploratory data analysis of the asthma patient dataset |
| `Projet1/` | Ensemble methods | Bagging, boosting, and random forest models |
| `Projet2/` | Regression | Linear/non-linear regression for exacerbation prediction |
| `Projet3/` | Synthetic data | Avatar method : privacy-preserving synthetic data generation |

## Data

- `Data/trainDataDC2.csv.gz` : compressed training set (2 089 rows, asthma patients)
- `Data/description_variables.md` : full variable dictionary
- **Target variable:** `post_index_exacerbations365` (number of exacerbations in the follow-up year)
- Key predictors: demographics, comorbidities, SABA canister usage, healthcare costs (raw + log-transformed)

## Outputs

- `outputs/` : CSV prediction files from each project phase
- `models/final_model.pkl` : serialized final trained model

## Tech stack

- **Python 3.12**, package manager: `uv`
- **ML:** scikit-learn, xgboost, imbalanced-learn, optuna
- **Data:** pandas, polars, numpy, pyarrow
- **Stats:** statsmodels, scipy
- **Viz:** seaborn, altair, plotly
- **Dimensionality reduction:** prince (MCA/PCA)
- **Linting:** ruff (rules E, F, I)

## Key concepts

**Avatar method** (`Projet3/`, `Note/Method-avatar.md`): generates synthetic patient records that preserve statistical properties while protecting privacy. Privacy is evaluated with DCR, NNDR, local cloaking, and hidden rate metrics. See also `docs/Guillaudeux et al. 2023.pdf`.

## Notes & documentation

Research notes are in `Note/` (markdown). Key files:
- `Article-analyse.md` : paper analysis on data anonymization
- `Method-avatar.md` : Avatar method deep-dive
- `CART.md`, `Test-stat.md`, `outlier-detect.md` : method notes

Pipeline visualization: open `docs/pipeline.html` in a browser (or via the http.server above).
