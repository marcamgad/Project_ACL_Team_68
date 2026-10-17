# F1 Podium Prediction Companion

Predicting whether a Formula 1 driver will finish on the podium (top 3) using only information available **after qualifying and before the race starts**.

**Team 68**

| ID | Name |
|---|---|
| 58-7289 | Marc Amgad |
| 58-7949 | Mark Baher Zaki |
| 58-4231 | Mario Nader |
| 58-2192 | David Maged |

## Repository contents

| File | Description |
|---|---|
| `f1_podium_prediction.ipynb` | Full Run-All notebook: audit, cleaning, data-engineering answers, features, models, experiments, XAI, inference function |
| `F1_Podium_Prediction_Report.pdf` | Analytical report |
| `requirements.txt` | Python dependencies |

## Dataset

[Formula 1 World Championship (1950–2024) — Ergast data on Kaggle](https://www.kaggle.com/datasets/rohanrao/formula-1-world-championship-1950-2020): 14 relational CSV tables.

- **Unit of analysis:** one driver entering one race (unique `raceId`, `driverId`)
- **Target:** podium = `positionOrder <= 3`
- **Prediction cutoff:** after qualifying, before the race start; no same-race results, status, points, laps or pit stops are used as features

## Notebook structure

1. **Phase 1 – Audit, EDA and cleaning:** `\N` sentinels, semantic typing, primary/foreign-key checks, shared-drive duplicates resolved to one row per driver-race, EDA before and after cleaning
2. **Phase 2 – Data-engineering questions**
   - Q1: front-row-to-podium conversion by circuit, before vs after 2014
   - Q2: home-country podium effect controlling for grid position
   - Q3: constructor mechanical-retirement rates, 2014–2021 vs 2022–2024
3. **Phase 3 – Feature engineering:** 17 leakage-safe features (grid, qualifying, previous standings, last-5 form), chronological split
4. **Phase 4 – Modelling:** baselines, Logistic Regression, Gradient Boosting, Random Forest, shallow neural network (Keras), soft-voting ensemble
5. **Phase 5 – Experiments:** feature-group ablation and three preprocessing-order experiments
6. **Phase 6 – Explainability:** permutation importance, SHAP (global and local), LIME
7. **§13 – Inference function:** `predict_podium()` demonstrated on a complete and a partially missing record

## Data split

| Split | Seasons | Rows |
|---|---|---|
| Train | 2003–2018 | 6,401 |
| Validation | 2019–2021 | 1,199 |
| Test | 2022–2024 | 1,356 |

## Main results (unseen test seasons 2022–2024)

| Model | ROC-AUC | PR-AUC | F1 |
|---|---|---|---|
| Baseline (grid ≤ 3) | 0.914 | 0.647 | 0.649 |
| Logistic Regression | 0.937 | 0.711 | 0.685 |
| Gradient Boosting | 0.937 | 0.707 | 0.685 |
| Random Forest | 0.938 | 0.706 | 0.675 |
| Shallow NN | 0.935 | 0.707 | 0.681 |
| **Soft-voting ensemble (selected)** | **0.938** | **0.711** | **0.681** |

- Primary metric: **PR-AUC** (podiums are only ~15% of rows); secondary: **F1** at a threshold chosen on validation
- The selected model beats the grid-only baseline by +0.063 PR-AUC (95% CI +0.023 to +0.100)
- Starting position (grid + qualifying) is the most important information; explanations describe what the model relies on, **not causal effects**

## How to run

**On Kaggle (recommended):** open the notebook, attach the Ergast F1 dataset, and click **Run All** (~30–40 minutes on CPU).

Kaggle notebook: <!-- add your public Kaggle link here -->

**Locally:**

```bash
pip install -r requirements.txt
export F1_DATA_DIR=/path/to/f1/csvs   # folder containing the 14 CSV files
jupyter notebook f1_podium_prediction.ipynb
```
