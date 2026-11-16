# Household Energy Analysis

A reproducible **methods demonstration** of data preparation, regression, classification, and exploratory clustering using household attributes.

**Dataset provenance is unverified.** The repository does not identify an original publisher, public dataset citation, or synthetic generator. Whether the records are real or synthetic, their units, and their measurement period are unknown. Results demonstrate a workflow; they are not validated real-world efficiency findings. See [provenance and verification requirements](data/PROVENANCE.md).

## Data preparation

The original [CSV](data/household_energy_unit_data.csv) is preserved unchanged: 1,000 rows and nine fields (`num_rooms`, `num_people`, `housearea`, `is_ac`, `is_tv`, `is_flat`, `num_children`, `is_urban`, `units`). The four `is_` fields are treated as binary flags; `units` is the consumption target, in unverified source units.

The notebook drops **9 rows with negative counts** and **13 additional rows with zero occupants**, leaving **978 rows**. Zero occupants cannot define per-person consumption; no replacement count is invented. No missing values or exact duplicates were found. Validation also checks fractional counts, nonpositive area, nonfinite values, negative consumption, and invalid binary flags. See the [excluded-row audit](reports/excluded_rows.csv).

The old all-numeric IQR filter is removed. It filtered on the target and target-derived ratios before splitting, and could discard valid binary categories. Plausible extremes are retained. Consequently, these results describe a different evaluation population from the previous 685-row run and should not be interpreted as a direct before/after model improvement.

## Evaluation

The [executed notebook](Household_Energy_Efficiency_Analysis.ipynb) uses a seeded 80/20 split: **782 training / 196 test rows**. Predictors are the eight raw household attributes. Consumption and its ratios are excluded from predictive features.

Regression selection uses five-fold cross-validation **within training data**. Linear regression was selected (mean CV RMSE 13.40 versus 13.91 for the forest). Test results are reported for transparency; the selection is not changed because a different model happens to score better on this test sample.

| Regression model | Test R² | Test RMSE | Test MAE |
| --- | ---: | ---: | ---: |
| Training-mean baseline | -0.011 | 26.50 | 20.71 |
| Linear Regression | 0.734 | 13.58 | 10.27 |
| Random Forest | 0.756 | 13.03 | 10.24 |

Errors use the CSV's consumption scale; **kWh is not verified**. The mean baseline predicts the training mean for every test row. Its test R² can be negative because the training and test means differ.

Classification predicts a **relative intensity proxy**, not audited efficiency: class 1 means consumption/area is at or below the **training-only** median (0.158948); class 0 is above it. This threshold is frozen for test labels.

| Classifier | Test accuracy |
| --- | ---: |
| Training-majority baseline | 47.45% |
| Random Forest | 86.22% |

The lift is **38.78 percentage points**. Training classes tie, so the dummy chooses class 0; the held-out class-0 share is 47.45%, explaining why the baseline is slightly below 50%. Both baseline and model per-class reports appear in the notebook and [metrics JSON](reports/metrics.json).

| Forest class / average | Precision | Recall | F1 | Support |
| --- | ---: | ---: | ---: | ---: |
| Higher intensity (0) | 0.851 | 0.860 | 0.856 | 93 |
| Lower intensity (1) | 0.873 | 0.864 | 0.868 | 103 |
| macro avg | 0.862 | 0.862 | 0.862 | 196 |
| weighted avg | 0.862 | 0.862 | 0.862 | 196 |

Confusion matrix, rows = actual and columns = predicted:

| Actual / predicted | Higher intensity (0) | Lower intensity (1) |
| --- | ---: | ---: |
| Higher intensity (0) | 80 | 13 |
| Lower intensity (1) | 14 | 89 |

## Error Analysis & Learnings

The notebook contains a dedicated section with residual plots, the ten largest misses, MAE and signed bias by AC status and occupant group (including sample counts), and misclassified rows with distance to the training threshold. [Held-out errors](reports/held_out_errors.csv) retain original CSV line references; these are not household IDs.

1. The largest regression miss is CSV line 898: actual minus predicted is **+43.43**, an underprediction. The AC=1 subgroup has MAE **12.84** across 76 held-out rows. Investigate missing weather, appliance-use, and duration information rather than delete difficult records; the dataset cannot establish the cause of these errors.
2. The forest makes **27 classification mistakes out of 196**. Wrong predictions have median intensity distance **0.00895** from the threshold. Inspect correct-versus-wrong distances in the notebook rather than assuming every error is a borderline case. Validate against measured efficiency labels before using this proxy for decisions.
3. Repeated permutation importance ranks **num_children** highest for regression and **housearea** for classification. Importance shows predictive associations, not causal effects; area also appears in the proxy label's denominator. Raw linear coefficient magnitudes are not used as comparable feature importances.

## Understand and reproduce the notebook

Every executable cell has a preceding explanation of why it exists and its assumptions. [CELL_GUIDE.md](CELL_GUIDE.md) maps all 12 executable cells to interview questions and practice exercises. It cannot substitute for personally running and explaining the code.

From the repository root, in a virtual environment:

```bash
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace Household_Energy_Efficiency_Analysis.ipynb
```

In Colab, open the notebook and run all; upload the bundled CSV when prompted. The notebook saves `reports/metrics.json`, `reports/excluded_rows.csv`, and `reports/held_out_errors.csv`. All 12 code cells were executed successfully, with plots embedded and validation assertions passing. Runtime versions are recorded in the metrics JSON; [requirements-tested.txt](requirements-tested.txt) records the direct dependencies used for this run. `requirements.txt` allows compatible version ranges; results can vary across versions.

## Limits and background

- The single random split assumes independent rows. There are no verified household, time, or geographic group identifiers to test generalization properly. A fixed seed is reproducibility, not uncertainty estimation.
- The held-out diagnostics and feature importance are post-hoc explanations. Further model tuning requires fresh evaluation data.
- Ratios do not adjust for climate, comfort, fuel mix, occupancy duration, or appliance behavior. Lower intensity is not proof of technical efficiency.
- K-means scales its ratio/size inputs, reports inertia and silhouette for k=2..6, and illustrates k=3 profiles. Three groups are not claimed to be natural or externally validated household types.
- Earlier notebook sections referenced unrelated columns and were rewritten. This revision adds explicit data rules, training-only model selection and label thresholds, baseline comparisons, and an auditable explanation of each cell. Ownership and understanding must be demonstrated in an interview, not inferred from an executed notebook.

MIT for repository code — see [LICENSE](LICENSE). Original dataset licensing provenance remains unverified.
