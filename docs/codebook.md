# Codebook — MultimodalClassification

This document describes all variables present in the data files.

---

## ⭐ Main Dataset: `data/raw/Data_Frame_VTQS.csv`

**This is the primary trial-by-trial long-format dataset.** It contains all 50 participants × 96 trials each = **4800 rows**, and is the recommended starting point for any analysis.

| Property | Value |
|----------|-------|
| Shape | 4800 rows × 13 columns |
| Format | Long format (one row = one trial) |
| Participants | 50 (IDs 1–50, including pre-exclusion outliers) |
| Trials per subject | 96 (48 per condition × 2 conditions) |
| Trials per Condition × StimType | 800 (16 per subject) |

### Variables

| Variable | Type | Values | Description |
|----------|------|--------|-------------|
| `ID` | int | 1–50 | Participant ID |
| `Trial_Index` | int | 1–64 | Trial number within a block |
| `Condition` | int | 1, 2 | Condition code (1=HAND, 2=CUBE) |
| `Condition_label` | str | `hand`, `cube` | Condition name |
| `Trial_Type` | int | 1, 3, 4 | Stimulation type code (1=OT, 3=SP, 4=DP) |
| `Trial_type_label` | str | `OT`, `SP`, `DP` | Stimulation type name |
| `Visual_stimulus` | int | 0–4 | Quadrant visually stimulated (0 = none, for OT trials) |
| `Tactile_stimulus` | int | 1–4 | Quadrant tactilely stimulated |
| `Felt_position` | int | 0–4 | Quadrant reported by participant (0 = no response, for OV trials) |
| `RT` | float | seconds | Raw reaction time (pedal press latency) |
| `log_RT` | float | log(seconds) | Natural log-transformed RT |
| `Error` | int | 0, 1 | 0 = correct localization, 1 = any error |
| `Visual_error` | float | 0.0, 1.0, NaN | 1 = vision-biased error (DP-V); 0 = other error (DP-L); NaN = non-DP trials |

### Notes on `Visual_error`
- Only populated for **DP trials** (where a visual stimulus was present on a different quadrant from touch)
- `Visual_error = 1.0` → **DP-V error**: participant reported the visually stimulated quadrant (visual capture of touch)
- `Visual_error = 0.0` → **DP-L error**: participant reported a quadrant that matched neither touch nor vision
- `Visual_error = NaN` → OT or SP trials (not applicable)

### Notes on `OV` trials
- `OV` (Only-Vision) trials are **not present** in this dataset — the localization task only included OT, SP, and DP. OV trials were part of the Ownership Task only.

---

## Naming Convention

Variable names follow the pattern:

```
{CONDITION}_{STIMTYPE}[_{EXTRA}]
```

| Token | Values | Description |
|-------|--------|-------------|
| `CONDITION` | `HAND`, `CUBE` | VR object shown to participant |
| `STIMTYPE` | `OV`, `OT`, `SQ`, `DQ` | Type of visuo-tactile stimulation |
| `EXTRA` | `CORR_z`, `ERR_z`, `_1`…`_6` | Sub-measure (see below) |

> **Note on naming:** In some files `SP` (Same Point) is coded as `SQ` and `DP` (Different Point) as `DQ`. These refer to the same stimulation types.

---

## Conditions

| Code | Full Name | Description |
|------|-----------|-------------|
| `HAND` | Hand condition | Virtual hand displayed 20 cm left of real hand |
| `CUBE` | Cube condition | Virtual cube displayed 20 cm left of real hand |

---

## Stimulation Types

| Code | Full Name | Touch | Vision | Notes |
|------|-----------|-------|--------|-------|
| `OV` | Only-Vision | ✗ | ✓ | Visual stimulus only, no tactile input |
| `OT` | Only-Touch | ✓ | ✗ | Tactile stimulus only, no visual input |
| `SP` / `SQ` | Same Point | ✓ | ✓ | Congruent: same quadrant touched and seen |
| `DP` / `DQ` | Different Point | ✓ | ✓ | Incongruent: different quadrants for touch and vision |

---

## Raw Per-Subject Files

### `data/raw/localization/per_subject/{ID}_{condition}.txt`

**Description:** Raw Unity output for the Localization Task, one file per subject × condition.  
**Format:** Space-separated, header on first line.

| Variable | Type | Values | Description |
|----------|------|--------|-------------|
| `Visual_stim` | int | 0–4 | Quadrant shown visually (0 = none, OT/OV trials) |
| `Tactile_stim` | int | 0–4 | Quadrant touched (0 = none, OV trials) |
| `Timestamp` | int | Unix ms | Absolute timestamp of stimulation |
| `Position` | int | 0–4 | Quadrant reported by participant (0 = no response) |
| `Strength` | int | 0, 4 | Vibration motor strength (0 = no vibration, 4 = active) |
| `Response_Time` | float | seconds | Reaction time (0 if no response; note: decimal separator is `,`) |

> **Note:** The first row of each file typically has `Strength=0` and `Response_Time=0`, indicating a baseline/initialisation trial. Decimal separator is a comma `,` (European format) — convert to `.` when parsing.

---

### `data/raw/drift/per_subject/{ID}_{condition}_d.txt`

**Description:** Raw Unity output for the Ownership/Drift Task, one file per subject × condition.  
**Format:** Space-separated, header on first line.

| Variable | Type | Values | Description |
|----------|------|--------|-------------|
| `StimulationType` | str | `OV`, `OT`, `SQ`, `DQ`, `0` | Type of stimulation block (0 = baseline ruler measure) |
| `RulerPosition` | float | 0.0–0.35 | Position of the ruler on screen (meters, Unity units) |
| `Distance` | float | meters | Distance between ruler position and reference point |
| `Answer` | int | cm | Participant's verbal report of hand position on ruler |

> **Note:** The first row (`StimulationType=0`) is the **pre-stimulation baseline** ruler measurement. Rows with a StimulationType code are the **post-stimulation** measures. Decimal separator is `,` — convert when parsing.

---

## File: `data/processed/drift/df_task2_drift_ownership.csv`

**Description:** Trial-by-trial long-format dataset for the Ownership/Drift Task (Task 1). Aggregated from the raw `_d.txt` files.  
**Shape:** 4000 rows × 6 columns  
**Participants:** 50 (all, including pre-exclusion outliers)

| Variable | Type | Values | Description |
|----------|------|--------|-------------|
| `participant` | int | 1–50 | Participant ID |
| `condition` | str | `hand`, `cube` | VR condition |
| `stimulation_type` | str | `OV`, `OT`, `SP`, `DP` | Stimulation type |
| `ruler_position` | float | meters | Position of ruler during measurement |
| `distance` | float | meters | Distance from reference point |
| `answer` | float | cm | Participant's reported hand position |

> Each row is **one ruler measurement** (both pre- and post-stimulation). To compute ΔPD, group by participant × condition × stimulation_type and calculate POST − PRE.

---

**Description:** Proprioceptive drift (ΔPD) for each participant, condition, and stimulation type.  
**Shape:** 45 rows × 11 columns  
**Unit:** centimeters (cm)

| Variable | Type | Description |
|----------|------|-------------|
| `Subject` | int | Participant ID (1–45) |
| `DRIFT_{CONDITION}_{STIMTYPE}` | float | ΔPD = POST_PP − PRE_PP for that condition × stim type |
| `DRIFT_HAND` | float | Mean drift across all stim types in HAND condition |
| `DRIFT_CUBE` | float | Mean drift across all stim types in CUBE condition |

---

## File: `DRIFT_POST_processed.xlsx`

**Description:** Mean POST-stimulation perceived hand position (ruler values) for each condition × stimulation type.  
**Shape:** 45 rows × 9 columns  
**Unit:** centimeters (cm) on the virtual ruler

| Variable | Type | Description |
|----------|------|-------------|
| `Subject` | int | Participant ID |
| `{CONDITION}_{STIMTYPE}` | float | Mean perceived hand position after stimulation |

---

## File: `SUBJECTIVE_processed.xlsx`

**Description:** Subjective ownership ratings (Likert scale) for each condition × stimulation type × question.  
**Shape:** 45 rows × 49 columns  
**Unit:** Likert scale 1–7 (1 = strongly disagree, 7 = strongly agree)

| Variable | Type | Description |
|----------|------|-------------|
| `Subject` | int | Participant ID |
| `{CONDITION}_{STIMTYPE}_{Q}` | int | Rating for question Q (1–6) in that condition × stim type |

### Questionnaire Items (based on Pavani et al., 2000 + 1 added item)

| Question | Code | Content | Role |
|----------|------|---------|------|
| Q1 | `_1` | "I felt as if the virtual hand/cube was my hand" | Ownership |
| Q2 | `_2` | Added item — ownership-related | Ownership |
| Q3 | `_3` | Ownership-related item | Ownership |
| Q4 | `_4` | Control question | Control |
| Q5 | `_5` | Control question | Control |
| Q6 | `_6` | Control question | Control |

> Q4–Q6 are control items for task compliance and suggestibility. Their analysis is reported in the Supplementary Materials.

---

## File: `LOCALIZATION_DRIFT_processed.xlsx`

**Description:** Tactile localization error counts and proprioceptive drift, combined per participant.  
**Shape:** 45 rows × 33 columns

| Variable | Type | Description |
|----------|------|-------------|
| `Subject_ID` | int | Participant ID |
| `{CONDITION}_OV` | int | N localization errors in OV (should be 0, false alarms) |
| `{CONDITION}_OT` | int | N errors in OT (pure tactile localization errors) |
| `{CONDITION}_SQ` | int | N errors in SP/SQ (congruent condition) |
| `{CONDITION}_TACTILE` | int | N errors in DP — tactile position (DP-L errors) |
| `{CONDITION}_VISUAL` | int | N errors in DP — visual position (DP-V, vision-biased errors) |
| `{CONDITION}_{STIMTYPE}_CORR_z` | float | Z-scored RT for **correct** trials |
| `{CONDITION}_{STIMTYPE}_ERR_z` | float | Z-scored RT for **error** trials |
| `DRIFT_{CONDITION}_{STIMTYPE}` | float | ΔPD = POST − PRE (cm) |

### Error Types for DP stimulation

| Code | Name | Description |
|------|------|-------------|
| `TACTILE` / `DP-L` | Localization error | Response matches neither touched nor visually stimulated quadrant |
| `VISUAL` / `DP-V` | Vision-biased error | Response matches the visually stimulated quadrant (visual capture) |

---

## File: `LOCALIZATION_RT_processed.xlsx`

**Description:** Tactile localization errors and intra-individually z-scored reaction times.  
**Shape:** 45 rows × 25 columns  
**Unit:** RT in z-scores (intra-individual standardization)

| Variable | Type | Description |
|----------|------|-------------|
| `Subject_ID` | int | Participant ID |
| `{CONDITION}_{STIMTYPE}` | int | N localization errors |
| `{CONDITION}_{STIMTYPE}_CORR_z` | float | Z-scored RT for correct trials |
| `{CONDITION}_{STIMTYPE}_ERR_z` | float | Z-scored RT for error trials |

> RT standardization: z-scores computed within each participant to account for individual baseline differences.

---

## Quadrant Map

The dorsum of the right hand was divided into 4 quadrants by a 5×5 cm cross:

```
        PROXIMAL
   ┌────────┬────────┐
   │   Q2   │   Q1   │  (Ulnar side = left in anatomical position)
   ├────────┼────────┤
   │   Q3   │   Q4   │
   └────────┴────────┘
        DISTAL
```

> Exact quadrant numbering should be verified against the original experimental protocol documentation.

---

## Participants

| Info | Value |
|------|-------|
| Recruited | 50 |
| Analyzed | 45 |
| Excluded | 5 (|z| > 2.86 on OT localization errors) |
| Age | M = 24.1 ± 3.2 years |
| Handedness | Assessed via Edinburgh Handedness Inventory |
| Dominant hand tested | Right |
