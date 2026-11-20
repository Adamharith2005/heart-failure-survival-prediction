# Heart Failure Survival Prediction

Predicting death of heart failure patients from routine clinical records using **Random Forest** and **Gradient Boosting**, with a fair benchmark against [Chicco & Jurman (2020)](https://doi.org/10.1186/s12911-020-1023-5).

![MCC comparison](images/mcc_comparison.png)

## Overview

- **Dataset:** [Heart Failure Clinical Records (UCI)](https://archive.ics.uci.edu/dataset/519/heart+failure+clinical+records), with 299 patients and 11 clinical features. The target is `DEATH_EVENT`, split 68% survived and 32% died.
- **Evaluation (same conditions as the paper):**
  - 100 random 80/20 splits, reported as mean ± standard deviation
  - `time` (follow-up period) excluded
  - every model evaluated on the same splits
- **Pipeline:**
  1. Baseline
  2. SMOTE, applied inside the training data only
  3. Nested hyperparameter tuning, scored on MCC
- **Metrics:** MCC (main metric), F1, Accuracy, Recall and ROC AUC.

## Results

Mean over 100 splits:

| Model | MCC | F1 | Accuracy | Recall | ROC AUC |
|---|---|---|---|---|---|
| Paper: Random Forest | 0.384 | 0.547 | 0.740 | 0.491 | 0.800 |
| Paper: Gradient Boosting | 0.367 | 0.527 | 0.738 | 0.477 | 0.754 |
| RF: baseline | 0.361 | 0.525 | 0.737 | 0.467 | 0.783 |
| GB: baseline | 0.347 | 0.527 | 0.728 | 0.489 | 0.742 |
| RF: SMOTE + tuned | 0.360 | 0.557 | 0.722 | 0.557 | 0.768 |
| **GB: SMOTE + tuned** | **0.363** | **0.562** | 0.723 | **0.568** | 0.752 |

## Key Findings

- **The paper's results were reproduced.** Both baselines fall within one standard deviation of the published results on every metric.
- **SMOTE + tuning raises recall from under 0.50 to about 0.56** (p < 0.001). This means more of the patients who died are correctly flagged, at a small cost in accuracy.
- **No method significantly improves MCC.** With 299 patients, the features limit performance more than the algorithm does.
- **Serum creatinine and ejection fraction** are the strongest predictors, which agrees with the paper.
- **Including `time` inflates accuracy from about 74% to 84%.** This is target leakage, because follow-up ends when a patient dies. It also explains the earlier version's single-split result of "83% accuracy".

## Limitations

- **Small, single-hospital dataset.** It has 299 patients from one hospital, recorded in 2015, and there is no external validation.
- **Fixed decision threshold of 0.5.** Adjusting the threshold could trade false alarms for missed deaths further.

## How to Run

```bash
git clone https://github.com/Adamharith2005/heart-failure-survival-prediction.git
cd heart-failure-survival-prediction
pip install -r requirements.txt
jupyter notebook notebooks/heart_failure_survival_prediction.ipynb
```

## About

This is an extended version of a group assignment for a Data Analytics course at Universiti Malaya. The original version was built by Muhammad Adam Harith bin Mohd Zaki, Nazhan Fahim bin Mohammad Najib, Amirul Hisyam bin Amir Ruddin, Iman Amirul Hakim bin Badlishah, Muaz Zikry bin Syamsul and Rauf bin Zaidi. I reworked the evaluation into a fair, repeated-split benchmark. The original submission is kept in [`archive/`](archive/) for comparison.