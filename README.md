# MultimodalClassification

## Overview

This repository contains data and analysis scripts for a **multimodal tactile localization experiment** investigating the role of body ownership on visuo-tactile integration, using a Virtual Reality (VR) setup. The paradigm is based on visual capture of touch and is conceptually related to the Rubber Hand Illusion.

**Institution:** University of Turin  
**Ethics:** Approved by the Ethical Committee of the University of Turin

---

## Experiment Summary

Fifty healthy participants (final N = 45 after outlier removal) took part in the study. Each participant wore an Oculus Quest 2 VR headset and placed their right hand on a marked surface. The dorsum of the hand was divided into four quadrants, each equipped with a vibration motor (Arduino-controlled).

### Conditions
| Condition | Description |
|-----------|-------------|
| `HAND` | A virtual hand appeared 20 cm to the left of the real hand |
| `CUBE` | A virtual cube appeared 20 cm to the left of the real hand |

### Stimulation Types
| Code | Name | Description |
|------|------|-------------|
| `OV` | Only-Vision | Visual stimulus on VR object only, no touch |
| `OT` | Only-Touch | Tactile stimulus on real hand only, no visual |
| `SP` | Same Point | Visual and tactile stimuli on the **same** quadrant |
| `DP` | Different Point | Visual and tactile stimuli on **different** quadrants |

### Tasks
1. **Ownership Task (Task 1):** Measures proprioceptive drift (ruler task) and subjective ownership (Likert 1–7 questionnaire, 6 questions) for each stimulation type × condition.
2. **Localization Task (Task 2):** Measures tactile localization errors and reaction times (pedal press) for each stimulation type × condition.

---

## Repository Structure

```
MultimodalClassification/
├── data/
│   ├── raw/                                      # Original unprocessed data from Unity/Arduino
│   │   ├── localization/
│   │   │   └── per_subject/                      # One .txt per subject × condition (98 files)
│   │   │       ├── 1_hand.txt                    # Format: {ID}_{condition}.txt
│   │   │       ├── 1_cube.txt
│   │   │       └── ...
│   │   ├── drift/
│   │   │   └── per_subject/                      # One .txt per subject × condition (100 files)
│   │   │       ├── 1_hand_d.txt                  # Format: {ID}_{condition}_d.txt
│   │   │       ├── 1_cube_d.txt
│   │   │       └── ...
│   │   ├── subjective/                           # (empty – subjective data collected via SPSS)
│   │   └── Data_Frame_VTQS.csv                   # ⭐ Main trial-by-trial dataset (N=50, 4800 rows)
│   │
│   └── processed/                                # Cleaned, analysis-ready data
│       ├── localization/
│       │   ├── Data_Frame_VTQS.csv               # Trial-by-trial, N=50 (copy of raw master)
│       │   ├── df_task1_localization.csv         # With z_RT, combo, tactile_error (N=50)
│       │   ├── LOCALIZATION_DRIFT_processed.xlsx # Aggregated errors + ΔPD, N=45 (no outliers)
│       │   └── LOCALIZATION_RT_processed.xlsx    # Aggregated errors + z-scored RT, N=45
│       ├── subjective/
│       │   ├── subjective_data.csv               # Wide format, all Q1–Q6, N=50
│       │   └── SUBJECTIVE_processed.xlsx         # Wide format, Q1–Q6, N=45 (no outliers)
│       └── drift/
│           ├── df_task2_drift_ownership.csv      # Trial-by-trial ruler measures, N=50 (4000 rows)
│           ├── DRIFT_processed.xlsx              # ΔPD aggregated per Condition × StimType, N=45
│           └── DRIFT_POST_processed.xlsx         # POST hand position aggregated, N=45
│
├── analysis/
│   ├── preprocessing/                # Data cleaning and outlier removal scripts
│   └── stats/
│       └── vtqs_analysis.py          # Main analysis script (RF, clustering, logistic regression)
├── results/
│   ├── figures/                      # Output plots and graphs
│   └── tables/                       # Output summary tables
└── docs/
    └── codebook.md                   # Variable descriptions and naming conventions
```

---

## Data Files Description

### ⭐ Primary dataset (trial-by-trial, long format)

| File | N subjects | N rows | Description |
|------|-----------|--------|-------------|
| `data/raw/Data_Frame_VTQS.csv` | 50 | 4800 | **Main dataset** — Localization Task only (OT, SP, DP). RT, log_RT, Error, Visual_error. |
| `data/processed/drift/df_task2_drift_ownership.csv` | 50 | 4000 | **Ownership/Drift Task** — trial-by-trial ruler measures per StimType × Condition. |

### Raw per-subject files

| Folder | N files | Format | Description |
|--------|---------|--------|-------------|
| `data/raw/localization/per_subject/` | 98 | `{ID}_{condition}.txt` | Raw Unity output — Localization Task |
| `data/raw/drift/per_subject/` | 100 | `{ID}_{condition}_d.txt` | Raw Unity output — Ownership/Drift Task |

### Processed / aggregated datasets (wide format, N=45 after outlier removal)

| File | N | Variables | Notes |
|------|---|-----------|-------|
| `data/processed/localization/df_task1_localization.csv` | 50 | trial-by-trial + z_RT, combo, tactile_error | Pre-outlier-removal version |
| `data/processed/subjective/subjective_data.csv` | 50 | Q1–Q6 Likert wide format | Pre-outlier-removal version |
| `data/processed/drift/DRIFT_processed.xlsx` | 45 | ΔPD per Condition × StimType | Formula: POST − PRE |
| `data/processed/drift/DRIFT_POST_processed.xlsx` | 45 | POST hand position per Condition × StimType | Raw ruler values |
| `data/processed/subjective/SUBJECTIVE_processed.xlsx` | 45 | Q1–Q6 Likert scores per Condition × StimType | Q4–Q6 are control items |
| `data/processed/localization/LOCALIZATION_DRIFT_processed.xlsx` | 45 | Localization errors + ΔPD | Combined file |
| `data/processed/localization/LOCALIZATION_RT_processed.xlsx` | 45 | Localization errors + z-scored RT | Intra-individual standardization |

---

## Outlier Removal

5 out of 50 participants were excluded based on z-scores of localization errors in the `OT` condition (threshold: |z| > 2.86), indicating unreliable tactile detection performance.

---

## Statistical Analyses

| Measure | Test | Post-hoc |
|---------|------|----------|
| Proprioceptive Drift | 2×4 repeated-measures ANOVA | Bonferroni |
| Subjective Ownership | Friedman test | Wilcoxon + Bonferroni |
| Localization Errors | Friedman test | Wilcoxon + Bonferroni |
| Reaction Times | Friedman test | Wilcoxon + Bonferroni |
| Ownership ~ DP-V errors | Spearman correlation | — |

---

## Citation

> Manuscript in preparation. Please contact the authors before using this data.
