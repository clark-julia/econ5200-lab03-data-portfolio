# econ5200-lab03-data-portfolio
# Pipeline Health Check — EDA, Corruption & Distribution Shift

## Objective
Diagnose and repair data-quality failures in a merged country-level economic panel using exploratory analysis alone, then quantify and monitor distribution shift between training and inference data.

## Methodology
- **Manual EDA diagnosis.** Inspected summary statistics, histograms, and scatter plots to find five planted corruptions before any modeling: negative GDP values, life expectancy stored in months rather than years, duplicated rows, percentage fields mixing units (fractions vs. percent), and a GDP unit mismatch (a factor-of-1,000 scale error).
- **Principled cleaning.** Chose thresholds from the observed data (for example, the gap separating legitimate GDP from mis-scaled rows). This reduced the panel from 260 to 230 rows.
- **Distribution-shift measurement.** Split the data proportionally into training (138 rows) and inference (92 rows) sets and computed the Population Stability Index (PSI) on each numeric column, using 10 bins with epsilon smoothing.
- **Manual vs. automated profiling.** Ran ydata-profiling on the corrupted dataset and compared its output with my manual findings to identify what each approach caught and missed.
- **Reusable tooling.** Packaged `eda_utils.py` with impossible-value checks, a PSI-based shift detector, and a dataset summary function, each validated with assertions.
- **Interactive dashboard.** Built a pipeline-health dashboard with training-vs-inference distribution comparison, an adjustable PSI threshold, constraint filters for impossible values, and a before/after view of the corrupted and cleaned data.

## Key Findings
- All five planted corruptions were identifiable through EDA alone, and each was fixed and documented.
- **GDP showed significant distribution shift** (PSI > 2 in the primary analysis; 1.76–2.46 in the dashboard depending on the split), far above the conventional 0.25 alert level. This matches the 1.3× shift deliberately applied to GDP.
- Population was stable (PSI ≈ 0.09), while life expectancy (≈ 0.16) and trade percentage (≈ 0.23) showed moderate shift. At this small sample size (138 vs. 92 rows), PSI is noisy, so the standard 0.10 / 0.25 thresholds should be recalibrated, ideally against PSI from random splits, before being used for production alerting.
- PSI measures shift in a column's distribution, so it would not catch the corruptions themselves (duplicates, for example). Row-level validation and distribution monitoring are complementary checks.
