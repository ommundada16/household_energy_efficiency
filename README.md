# Household Energy Analysis

A machine learning methods demonstration covering data preparation, consumption prediction, relative-intensity classification, and exploratory household clustering.

## Dataset

The bundled [CSV](data/household_energy_unit_data.csv) contains 1,000 rows with household counts, area, appliance/location flags, and a consumption target (`units`). Its original source, real-versus-synthetic status, measurement period, and energy/area units are **unverified**. Results use source units and demonstrate methods rather than validated real-world efficiency findings. See [dataset provenance](data/PROVENANCE.md).

## Data preparation

- Dropped 9 rows with negative counts and 13 additional rows with zero occupants, leaving **978 valid rows**.
- Checked missing/nonfinite values, fractional counts, nonpositive area, negative consumption, binary flags, and duplicates. No missing values or duplicates were found.
- Calculated per-person and per-area consumption after validation. No count imputation or IQR filtering was applied; plausible extremes were retained.
- Preserved the raw CSV and saved an [excluded-row audit](reports/excluded_rows.csv).

## Methods and results

The [executed notebook](Household_Energy_Efficiency_Analysis.ipynb) uses a seeded 80/20 split: **782 training / 196 test rows**. Models use eight raw household attributes; consumption-derived ratios are excluded from predictive features.

### Regression

Five-fold cross-validation on training data selected linear regression (mean CV RMSE 13.40 versus 13.91 for random forest). Test results are reported for both models without changing the selection.

| Model | Test R² | Test RMSE | Test MAE |
| --- | ---: | ---: | ---: |
| Training-mean baseline | -0.011 | 26.50 | 20.71 |
| Linear regression (selected) | 0.734 | 13.58 | 10.27 |
| Random forest | 0.756 | 13.03 | 10.24 |

### Classification

Class 1 indicates consumption/area at or below the **training-only median** (0.158948); class 0 indicates higher intensity. This is a relative-intensity proxy, not an audited efficiency rating.

| Classifier | Test accuracy |
| --- | ---: |
| Training-majority baseline | 47.45% |
| Random forest | 86.22% |

The lift is **38.78 percentage points**. Training classes tie, so the baseline chooses class 0, which accounts for 47.45% of test rows.

| Forest class / average | Precision | Recall | F1 | Support |
| --- | ---: | ---: | ---: | ---: |
| Higher intensity (0) | 0.851 | 0.860 | 0.856 | 93 |
| Lower intensity (1) | 0.873 | 0.864 | 0.868 | 103 |
| Macro average | 0.862 | 0.862 | 0.862 | 196 |
| Weighted average | 0.862 | 0.862 | 0.862 | 196 |

Confusion matrix (rows = actual, columns = predicted):

| Actual / predicted | Higher intensity (0) | Lower intensity (1) |
| --- | ---: | ---: |
| Higher intensity (0) | 80 | 13 |
| Lower intensity (1) | 14 | 89 |

### Clustering

K-means uses standardized consumption ratios and household size attributes. The notebook compares inertia and silhouette for k=2..6 and illustrates k=3 profiles; these are exploratory groups, not externally validated household types.

## Error Analysis & Learnings

The notebook includes residual plots, largest misses, subgroup MAE/bias with sample counts, misclassified rows, and repeated permutation importance. [Held-out errors](reports/held_out_errors.csv) reference original CSV lines.

1. The largest regression miss is CSV line 898: **+43.43** actual minus predicted, indicating underprediction. The AC=1 subgroup has MAE **12.84** across 76 test rows. Weather, appliance-use, and measurement-duration data could help investigate these errors; their causes cannot be established here.
2. The classifier makes **27 errors out of 196**, with median intensity distance **0.00895** from the threshold among mistakes. Correct/wrong distance comparisons are reported in the notebook. Measured efficiency labels would be needed to validate the proxy.
3. Permutation importance ranks **num_children** highest for regression and **housearea** for classification. These are predictive associations; area importance also reflects its role in label construction.

## Run

From the repository root:

```bash
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace Household_Energy_Efficiency_Analysis.ipynb
```

In Colab, run the notebook and upload the bundled CSV when prompted. All 12 code cells have executed successfully with embedded plots and validation assertions. [Metrics and runtime versions](reports/metrics.json), exclusions, and held-out errors are regenerated under `reports/`. [requirements-tested.txt](requirements-tested.txt) records direct dependencies used for the committed run.

## Limitations

Dataset provenance and units are unverified. A single random split assumes independent records and does not establish performance across locations or periods. Relative intensity does not adjust for climate, comfort, fuel mix, or occupancy duration. Error analysis and importance are post-hoc; further tuning requires fresh evaluation data.

## License

[MIT](LICENSE) for repository code. Original dataset licensing provenance remains unverified.
