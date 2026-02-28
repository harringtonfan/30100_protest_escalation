# Protest Escalation into Violence (C227) — Project Repo

This repository hosts my **full course project** on predicting **protest escalation into violence** using ACLED events, Nighttime Lights (NTL) extracted from Google Earth Engine (GEE), and V-Dem political context variables.

✅ **Current status:** this repo is at the **check-in milestone** stage (EDA + baseline + initial supervised models + early error analysis).  
➡️ **Next:** I will continue updating this same repo for the **final submission**, including additional models, deeper evaluation/interpretation, and a cleaner reproduction pipeline.

---

## 1) Repository structure (current)
.
├── data/
│   ├── events_2021_for_gee.csv
│   ├── events_2021_with_ntl_2020_5km.csv
│   ├── events_2022_for_gee.csv
│   ├── events_2022_with_ntl_2021_5km.csv
│   ├── events_2023_for_gee.csv
│   ├── events_2023_with_ntl_2022_5km.csv
│   ├── events_2024_for_gee.csv
│   └── events_2024_with_ntl_2023_5km.csv
├── group_project_final.ipynb
├── C227_protest_escalation_checkin.pdf
└── .gitignore

> The `data/` folder currently contains **only the lightweight CSVs** needed to document the workflow and support the check-in notebook.

---

## 2) Data sources (conceptual)

- **ACLED**: event-level protest records; used to define the binary target `y_binary`  
- **Nighttime Lights (NTL)**: extracted via **Google Earth Engine** as summary statistics within a **5km buffer** around each event  
- **V-Dem**: country-year political context indicators (e.g., `v2x_libdem`, `v2x_civlib`)

---

## 3) Large files not committed (size constraints)

Due to GitHub file-size limits, several large datasets/intermediate artifacts are **not tracked** in this repo, including:

- Full raw ACLED download / large intermediate merged tables
- Full V-Dem raw file (wide format) and large processed versions
- Cached intermediate merges and some derived artifacts

### How to reproduce despite missing large files
- The committed event CSVs + NTL-merged CSVs preserve the **key join structure** and allow most modeling steps to run.
- For full reproduction, you need local copies of the large source files (e.g., V-Dem) and/or regenerate them.
- If necessary, update file paths at the top of the notebook.

*(Final version plan: I will add a clearer data pipeline section and, if allowed, provide download instructions or scripts for regenerating large files.)*

---

## 4) Scope (in-sample restriction used for modeling)

To ensure stable covariate coverage (especially NTL), the modeling dataset is restricted to:

- **Years:** 2021–2024  
- **Countries:** Argentina, Brazil, Chile, Colombia, Mexico, Peru

This restriction dramatically reduces missingness in NTL/V-Dem merges and improves comparability across countries.

---

## 5) Target definition

Binary label:
- `y_binary = 1` → violent  
- `y_binary = 0` → peaceful  

The dataset is imbalanced (violent events are a minority), so evaluation emphasizes **Precision/Recall/F1** and **PR-AUC**, not accuracy alone.

---

## 6) Features (current)

### Context covariates
- V-Dem: `v2x_libdem`, `v2x_civlib`
- NTL (5km): `ntl_mean_5km`, `ntl_p90_5km`, `ntl_std_5km` (log1p transformed)

### Time features
- `month`, `dow`, `weekofyear`

### History / diffusion (leakage-controlled)
Rolling historical features computed with `shift(1)` so each event uses only **past information**:
- country-level: `ctry_past_30D_count`, `ctry_past_30D_violent_share`, `ctry_past_90D_count`, `ctry_past_90D_violent_share`
- (optional) city-level analogues (if enabled later)

---

## 7) Split strategy

To mimic prediction over time and avoid temporal leakage:

- **Train:** 2021–2022  
- **Validation:** 2023  
- **Test:** 2024 (held-out)

Class balance (violent share) is reported for each split.

---

## 8) Models (current milestone)

### Supervised (required)
- Dummy baseline (`most_frequent`)
- Logistic Regression (linear; tuned via `C`)
- Random Forest (tree-based; tuned via `max_depth`, `min_samples_leaf`, `n_estimators`)

### Unsupervised (planned for final)
- PCA / clustering-based exploration will be added in the final version if helpful for structure discovery.

Evaluation metrics:
- Precision / Recall / F1
- PR-AUC (most informative under imbalance)
- ROC-AUC (ranking quality; supplementary)

---

## 9) How to run

Open `group_project_final.ipynb` and run top-to-bottom.

Typical dependencies:
- pandas, numpy, matplotlib
- scikit-learn

---

## 10) Roadmap toward final submission

Planned improvements before final:
- Add a clearer reproducible **data pipeline** (scripts or well-documented steps)
- Add at least one **unsupervised** component (e.g., PCA + clustering) if it improves interpretation
- Expand feature engineering + diagnostics (e.g., calibration, threshold selection, subgroup analysis)
- Strengthen interpretation: coefficients, feature importance, and structured error analysis tied to the research question
- Clean notebook → final narrative structure + figures

---

## 11) Author

Harrington Fan
