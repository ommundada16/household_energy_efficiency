# Explain every notebook cell

Each executable cell has a preceding rationale in the notebook. This guide is a rehearsal aid, not evidence of mastery. Run the analysis and answer these questions in your own words before presenting it in an interview.

| Section / executable cell | Purpose | Be ready to explain |
| --- | --- | --- |
| 1. Setup | Imports, seeds, versions. | Repeatability versus reliability across samples. |
| 2. Load | Schema check, raw preservation, CSV line references. | Why a line number is not a household ID; unknown origin and units. |
| 3. Clean | Drop explicit invalid values and exact duplicates before ratios. | Negative versus zero counts, zero denominators, dropping versus imputation, overlapping rule counts versus unique exclusions. |
| 4. EDA | Plot cleaned distributions and associations. | Why correlation is not causation and consumption-derived ratios leak the regression target. |
| 5. Split | Hold out 20%; derive median label threshold on training rows. | Why a full-data median uses test information; why test classes need not be exactly balanced. |
| 6. Select regression | Five training-only folds compare linear regression and forest by RMSE. | Why selecting on test R² is optimistic; why we take the square root per fold before averaging. |
| 7. Regression metrics | Compare models with the training-mean dummy on identical test rows. | MAE versus RMSE; negative R²; why the dummy's test R² need not be zero. |
| 8. Classification | Compare training-majority dummy and forest, both-class reports and confusion matrices. | False positives/negatives with class 1 = lower intensity; precision, recall, F1, support, macro/weighted averages, zero-division handling. |
| 9. Errors | Largest residuals, subgroup support and errors, wrong labels and threshold distances. | Positive residual means underprediction; small subgroup uncertainty; close versus distant classification mistakes. |
| 10. Importance | Repeated feature shuffles and three computed takeaways. | Why raw coefficient magnitudes mix units; correlated features share importance; area importance can reflect label construction. |
| 11. Clustering | Scale features, compare k=2..6 diagnostics, illustrate k=3. | Euclidean distance, inertia versus silhouette, why three groups are illustrative rather than proven natural types. |
| 12. Report | Assertions, metrics, exclusion audit, and held-out errors. | Which assumptions checks verify and which they cannot; why edited results must be regenerated. |

## Practice tasks

1. Calculate accuracy and class-1 precision/recall/F1 from the confusion matrix by hand. Reconcile with the report and explain the dummy's missing-class behavior.
2. Change the seed on an experimental copy and discuss variation without cherry-picking the best score. Explain repeated-split uncertainty and the value of external validation.
3. Explain why preserving valid extremes improves the evaluation population. The old IQR rule could remove every valid zero from a binary column with Q1=Q3=1 and also filtered on the target.
4. Walk through a large regression miss and a wrong classification by CSV line. Describe observed facts without inventing causes; suggest missing measurements that could help.
5. Explain how a large high-consumption house can receive a lower-intensity label. Why is that not audited efficiency, comfort-adjusted performance, or carbon performance?
6. Explain why post-hoc test diagnostics cannot become tuning data while retaining the same test-performance claim. What new split or dataset would you use next?

## Honest project description

“Built a reproducible household-consumption methods demonstration on a bundled dataset of unverified origin, with explicit validity checks, baseline comparisons, held-out regression/classification metrics, and error analysis.”

Add scores only with their baseline and evaluation setup. Do not claim understanding you cannot demonstrate or describe this dataset as verified real-world evidence.
