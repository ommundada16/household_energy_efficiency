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

## Results

This notebook has been executed end-to-end (all 55 cells, zero errors) — the
committed `.ipynb` has real outputs baked in, so every plot and metric below
renders directly on GitHub without anyone needing to run anything:

| Model | Result |
|---|---|
| Regression (Linear Regression beat Random Forest) | **R² = 0.738**, RMSE = 12.01 kWh |
| Strongest energy-use predictor | `is_ac` |
| Classification (Random Forest) | **86.1% accuracy** (efficient vs. inefficient) |
| Strongest efficiency predictor | `housearea` |
| Clustering (K-means) | 3 distinct household consumption profiles |

(1,000 raw records → 685 after IQR-based outlier removal.)

## Running it

Outputs are already committed, so you don't need to run anything just to see
the results — but to reproduce or modify the analysis:

**In Google Colab**: open the notebook, run all cells, and use the file-upload
prompt near the top to upload `data/household_energy_unit_data.csv`.

**Locally / Jupyter**: clone this repo and run from its root — the notebook
automatically falls back to reading `data/household_energy_unit_data.csv` when
it's not running in Colab.

```
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace Household_Energy_Efficiency_Analysis.ipynb
```

## Known limitations

- **Data quality**: a handful of rows have `-1` in `num_rooms` or `num_people`
  (evidently data-entry errors — these are counts that can't be negative). The
  IQR outlier filter doesn't fully catch them since `-1` falls within the
  statistical bounds for those columns. They're left as-is rather than
  silently dropped, so results above may be marginally affected by a small
  number of invalid rows.
- **Package versions are pinned defensively**: `pandas<3.0` and `seaborn<0.14`
  in `requirements.txt` avoid two forward-compatibility breaks — pandas 3.x's
  copy-on-write makes `df[col].fillna(..., inplace=True)` a silent no-op instead
  of actually filling values, and newer Seaborn turns `palette=` without a `hue`
  argument (used in several plotting cells here) from a warning into a hard
  error. Neither affects this specific dataset today (it has zero missing
  values), but both would bite on a slightly different dataset or a future
  package upgrade.

## Background

This started from a version of the notebook where Part 2 onward (Regression,
Classification, Clustering) referenced columns from a completely different
dataset (`Global_active_power(kW)`, `Household_ID`, `Efficiency_Label`, etc.)
that don't exist in this project's actual data — those sections would have
thrown `KeyError` immediately if run. Parts 2–4 here are a rewrite that actually
runs against the real `household_energy_unit_data.csv` columns.

Part 1 (preprocessing) kept its original logic, but wasn't bug-free either: its
"feature vs target" scatter-grid cell hardcoded a 3×3 subplot layout (9 slots)
while trying to plot 11 non-target numeric columns, which throws
`ValueError: num must be an integer with 1 <= num <= 9`. That surfaced when
[@ommundada16](https://github.com/ommundada16) ran the original in Colab — the
grid here now sizes itself to however many columns there are instead of a
hardcoded count.

## License

MIT — see [LICENSE](LICENSE).
