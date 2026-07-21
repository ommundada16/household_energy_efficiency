# Household Energy Efficiency Analysis

A machine learning mini-project analyzing household electricity consumption:
predicting total energy usage, classifying households as efficient/inefficient,
and segmenting households into consumption profiles — all from basic structural
attributes (room count, occupants, house area, appliances, location).

## Dataset

[`data/household_energy_unit_data.csv`](data/household_energy_unit_data.csv) — 1,000
households, 9 columns:

| Column | Meaning |
|---|---|
| `num_rooms` | Number of rooms in the household |
| `num_people` | Number of occupants |
| `housearea` | House area (sq. m) |
| `is_ac` | Has air conditioning (0/1) |
| `is_tv` | Has a television (0/1) |
| `is_flat` | Is an apartment/flat vs standalone house (0/1) |
| `num_children` | Number of children in the household |
| `is_urban` | Located in an urban area (0/1) |
| `units` | Total energy consumed (kWh) — the prediction target |

## What the notebook does

[`Household_Energy_Efficiency_Analysis.ipynb`](Household_Energy_Efficiency_Analysis.ipynb)

**Part 1 — Preprocessing & EDA**: missing-value checks, feature engineering
(`Energy_per_Person`, `Energy_per_SqM`), distribution plots, correlation heatmap,
and IQR-based outlier removal (1,000 → 685 rows).

**Part 2 — Regression**: predicts total energy consumption (`Total_Energy_kWh`)
from household attributes, comparing Linear Regression against a Random Forest
Regressor and reporting R² / RMSE and feature importance for whichever performs
better.

**Part 3 — Classification**: since the raw data has no efficiency label, one is
derived — a household is "Efficient" if its energy use per square meter is at or
below the dataset median. A Random Forest Classifier then predicts that label
from the same structural attributes (not from energy data itself, to avoid
leaking the answer).

**Part 4 — Clustering**: K-means segments households into consumption profiles
using per-person and per-area energy use plus house size, with an elbow plot to
justify the choice of *k*.

A dynamically-computed summary (no hardcoded numbers) closes out the notebook.

## Running it

**In Google Colab**: open the notebook, run all cells, and use the file-upload
prompt near the top to upload `data/household_energy_unit_data.csv`.

**Locally / Jupyter**: clone this repo and run from its root — the notebook
automatically falls back to reading `data/household_energy_unit_data.csv` when
it's not running in Colab.

```
pip install -r requirements.txt
jupyter notebook Household_Energy_Efficiency_Analysis.ipynb
```

## Known limitations

- **Data quality**: a handful of rows have `-1` in `num_rooms` or `num_people`
  (evidently data-entry errors — these are counts that can't be negative). The
  IQR outlier filter doesn't fully catch them since `-1` falls within the
  statistical bounds for those columns. They're left as-is rather than
  silently dropped, so results may be marginally affected by a small number of
  invalid rows.
- **Not run end-to-end on this machine**: this notebook's logic was written and
  reviewed carefully (correct column references, no target leakage, valid
  scikit-learn API usage) but wasn't executed here due to a broken local Python
  installation (missing standard-library files unrelated to this project). It
  should run cleanly in Colab or any normal Python environment with the
  packages in `requirements.txt` — if you hit an error, it's worth an issue.

## Background

This started from a version of the notebook where Part 2 onward (Regression,
Classification, Clustering) referenced columns from a completely different
dataset (`Global_active_power(kW)`, `Household_ID`, `Efficiency_Label`, etc.)
that don't exist in this project's actual data — those sections would have
thrown `KeyError` immediately if run. Part 1 (preprocessing) was solid and
unchanged; Parts 2–4 here are a rewrite that actually runs against the real
`household_energy_unit_data.csv` columns.

## License

MIT — see [LICENSE](LICENSE).
