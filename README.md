# Honours-Project-Geophysics

# Forecasting Elevated Seismic Activity at Moab Khotsong Mine

**Annah Mpendulo Mlimi**  
BSc Honours in Computer Science, University of the Witwatersrand, 2026

This repository contains the implementation for my Honours research on forecasting seven-day elevated recorded seismic activity at Moab Khotsong Mine, South Africa.

The study compares Random Forest, XGBoost, Support Vector Machine (SVM) and Multilayer Perceptron (MLP) classifiers with statistical baselines. The original investigation concerned rockburst prediction. Since verified damaging-rockburst labels were unavailable for the experiments, the implemented outcome is elevated activity defined using event counts and recorded energy.

## Repository files

The notebooks and results are supplied as ZIP archives. Download and extract them before running the project.

| File | Contents and use |
| --- | --- |
| `notebooks.zip` | Final notebook workflow. Keep `workflow.py` and `candidate_configs.json` with the notebooks. |
| `results_final.zip` | Final experiment outputs. Use these results when examining the final model comparison. |
| `requirements.txt` | Python dependencies for the workflow. |
| `results.zip` | Earlier results retained for reference; use `results_final.zip` for the final experiments. |
| `figures.zip` | Earlier figure archive; use figures from the final results when interpreting the final experiments. |

## Dataset download

The dataset files are hosted separately on Google Drive:

- [Dataset download 1](https://drive.google.com/file/d/1iN5dJGYwtZjWflYMR0c7V9BwluifID-r/view?usp=sharing)
- [Dataset download 2](https://drive.google.com/file/d/1K1mj-og1SqeS2EetUAh6HU9NcEKRNGf3/view?usp=drive_link)

Download the supplied data files and extract any archives. Locate the raw event catalogue named **`Moabevpall.txt`** and place it at `Data/raw/Moabevpall.txt` within your local project folder. Access to the downloads depends on their Google Drive sharing settings; request access through Drive if prompted.

The catalogue used in the experiments contains 847,263 events spanning January 2000 to June 2020. The notebooks generate daily summaries and model inputs from the raw file.

## Local setup

1. Download or clone this repository.
2. Extract `notebooks.zip`. Arrange the extracted files so that the folder named `notebooks` sits directly inside your project folder. If extraction creates an additional enclosing folder, move the inner `notebooks` folder to this location.
3. Keep `workflow.py` and `candidate_configs.json` inside `notebooks`.
4. Place the raw catalogue at `Data/raw/Moabevpall.txt`. Lowercase `data` is also supported.
5. To inspect the saved experiment outputs, extract `results_final.zip` so that `results_final` sits beside `notebooks`.

The required paths, relative to your project folder, are:

| Path | Purpose |
| --- | --- |
| `requirements.txt` | Package requirements |
| `Data/raw/Moabevpall.txt` | Raw catalogue |
| `notebooks/01_data_loading.ipynb` through `notebooks/13_horizon_sensitivity.ipynb` | Analysis notebooks |
| `notebooks/workflow.py` | Shared paths, validation, model construction and evaluation functions |
| `notebooks/candidate_configs.json` | Model configurations evaluated during selection |
| `Data/processed_final/` | Generated daily data and modelling tables |
| `results_final/` | Saved predictions, scores, figures, models and run information |

The final two folders are created by the workflow if needed. Avoid accidentally nesting the data as `Data/Data/raw/` or the notebooks as `notebooks/notebooks/`.

## Install dependencies

The reference execution used Python 3.12. From the project folder, run:

```bash
python -m pip install -r requirements.txt
```

Open the project in VS Code with Python and Jupyter support, or use an existing Jupyter installation. Select the Python environment in which you installed the requirements.

## Run the notebooks

Run every cell in each notebook, following the order below. Restart the kernel between notebooks. Open them from the project root or the `notebooks` folder so the shared module can be located.

| Order | Notebook | Main task |
| --- | --- | --- |
| 01 | `01_data_loading.ipynb` | Load the raw catalogue |
| 02 | `02_data_understanding.ipynb` | Inspect fields and data completeness |
| 03 | `03_preprocessing.ipynb` | Prepare the event data |
| 04 | `04_exploratory_analysis.ipynb` | Explore temporal and spatial patterns |
| 05 | `05_daily_time_series.ipynb` | Aggregate events into daily observations |
| 06 | `06_feature_engineering.ipynb` | Construct historical predictors, targets and chronological partitions |
| 07 | `07_random_forest.ipynb` | Evaluate and fit Random Forest candidates |
| 08 | `08_xgboost.ipynb` | Evaluate and fit XGBoost candidates |
| 09 | `09_svm.ipynb` | Evaluate and fit SVM candidates |
| 10 | `10_mlp.ipynb` | Evaluate and fit MLP candidates |
| 11 | `11_statistical_baselines.ipynb` | Evaluate statistical baselines |
| 12 | `12_model_comparison.ipynb` | Compare predictions, metrics and diagnostic plots |
| 13 | `13_horizon_sensitivity.ipynb` | Examine target horizons and thresholds using training data |

Notebook 12 requires the outputs from notebooks 07–11. Notebook 13 is a separate sensitivity analysis. Rerunning the workflow overwrites generated files in `Data/processed_final` and `results_final`; retain a separate copy of downloaded reference results if you want to compare them with your run.

### Required support files

`workflow.py` contains functions reused across notebooks, including data paths, temporal validation folds, model construction and evaluation metrics. `candidate_configs.json` stores the settings tested for each model family. **Both files are required and must remain inside `notebooks`.**

## Method overview

The workflow constructs 24 predictors from observations preceding each forecast date. These include event-count lags and rolling summaries of count, moment, energy and corner frequency.

A forecast date is labelled elevated when the following seven days' event count or recorded energy reaches its training-derived 95th-percentile threshold. The final chronological partition contains 5,977 training dates and 1,496 test dates. Six overlapping training dates are removed at the boundary.

Model selection uses five expanding temporal validation folds with a six-date gap. Target thresholds and preprocessing are fitted using each fold's training data. The comparisons include majority-class, always-positive, recent-count, recent-energy, logistic-regression and seven-day-persistence baselines.

## Reading the results

The final comparison is saved in `results_final/comparison/all_test_metrics.csv`. The output folders also contain validation scores, saved predictions and diagnostic figures.

In the reference execution, seven-day persistence achieved test F1 of **0.8117**, compared with **0.7704** for the strongest ML classifier, the linear SVM. Random Forest had the strongest ROC AUC and average precision among the fitted ML models. These results show the importance of comparing model complexity with recent activity alone.

The evaluation is retrospective: the test period was inspected during development. Positive-label frequency changes substantially between training and testing, and neighbouring seven-day outcomes overlap. The results describe elevated recorded activity and should be interpreted within this scope.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| `No module named workflow` | Keep `workflow.py` beside the notebooks and open the project from its root or `notebooks` folder. |
| `candidate_configs.json` not found | Keep the configuration file inside `notebooks`. |
| Raw data file not found | Check that the file is exactly at `Data/raw/Moabevpall.txt`, without an extra enclosing folder. |
| Processed tables or model results missing | Run the preceding notebooks in order. For notebook 12, complete notebooks 07–11 first. |
| Missing Python package | Install `requirements.txt` into the environment selected by the notebook kernel. |
| Rerun scores differ | Compare package versions, selected configurations and saved predictions before replacing the reference results. |

## Acknowledgements

I thank Jonathan Kariv, Michael Westgate and Musa Manzi for supervision, and David and Richard Masethe for assistance with investigating and clarifying the catalogue fields. Details of research development and AI assistance are recorded in the accompanying research report.
