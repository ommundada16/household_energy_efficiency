# Dataset provenance

**Status: unverified.** The bundled `household_energy_unit_data.csv` contains 1,000 records and nine numeric fields, but no original publisher, source citation, collection protocol, or generator is documented. Its real-versus-synthetic status is unknown.

Field names suggest household counts, area, binary flags, and consumption. Energy/area units, measurement period, geography, collection date, sampling method, and original dataset permissions are undocumented. The repository's MIT license covers repository code; it does not independently establish the dataset's original licensing provenance.

This analysis is a methods demonstration on data of unverified origin. Results do not establish real-world household efficiency or causal effects.

The raw CSV is unchanged. Validity rules run before ratios or splitting, and exclusions retain CSV line references in `reports/excluded_rows.csv`. No count imputation or IQR deletion is applied. Row accounting and runtime versions are recorded in `reports/metrics.json`.
