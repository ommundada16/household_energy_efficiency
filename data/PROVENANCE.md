# Dataset provenance

**Status: unverified.** The repository bundles `household_energy_unit_data.csv` without an original publisher, source URL, collection protocol, or generator. Its existing documentation and history do not establish whether these are real households or synthetic examples. A public GitHub copy is a distribution location, not proof of origin.

Frame this project as a **methods demonstration on data of unverified origin**. Do not assert real-world findings or synthetic origin without evidence. Synthetic-looking distributions alone cannot establish provenance.

The CSV contains 1,000 rows and nine numeric fields: household counts, area, binary flags, and `units`. Area units, energy units, measurement period, geography, collection date, sampling method, and original data permissions are undocumented. Earlier documentation called the units square meters and kWh; those interpretations are unsubstantiated. The repository's MIT license does not independently establish the original dataset's licensing provenance.

The raw CSV remains unchanged. Explicit validity rules run before ratios or splitting; the notebook writes exclusions with CSV line references to `reports/excluded_rows.csv`. No count imputation or IQR outlier deletion is used. Exact accounting and runtime versions are in `reports/metrics.json`.

To resolve this status, obtain the original source citation and URL, license or permission statement, data dictionary with units and period, and a collection description or synthetic generator with seed. Update this document and rerun. If synthetic origin is verified, label it synthetic and discuss the generating relationships rather than population insights.
