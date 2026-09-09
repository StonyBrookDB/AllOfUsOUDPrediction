# AllOfUsOUDPrediction

Predicting opioid use disorder (OUD) from *All of Us* data, and testing whether
patient-reported **survey data** improve prediction beyond electronic health
records (EHR).

![Python 3.12](https://img.shields.io/badge/python-3.12-blue.svg)
![Data: All of Us](https://img.shields.io/badge/data-All%20of%20Us-orange.svg)

We compare EHR-only and EHR+survey models across 8 machine-learning
architectures and 6-, 12-, and 24-month look-back windows, on 267,747 *All of Us*
participants with documented opioid exposure (15,287 OUD cases). Adding survey
features improved PR-AUC in every model–window combination; the best 24-month
model (LightGBM) improved from 0.6219 to 0.6603.

## Data & access

**No patient data is included in this repository, and none can be shared.** All
data come from the *All of Us* Research Program (through Oct 1, 2023) and are
governed by its Data Use Agreement. Analyses run entirely inside the *All of Us*
Researcher Workbench. Reproducing this work requires your own approved Workbench
access — register at <https://www.researchallofus.org/register/>. For research
use only; not for clinical decision-making.

## Pipeline

Notebooks are run in order:

| Notebook | Purpose |
|---|---|
| `amia_data_download.ipynb` | Build the cohort and pull raw EHR + survey tables |
| `amia_data_prepare.ipynb` | Clean and normalize raw tables |
| `amia_feature2.ipynb` | Build EHR + demographic feature matrices (6/12/24-month windows) |
| `survey.ipynb` | Build survey feature matrices + the EHR+survey matrix |
| `AMIA_model_precision.ipynb` | Class-imbalance strategy comparison (choose imbalance handling) |
| `model.ipynb` | Train/evaluate all models × feature sets; save risk scores |
| `feature permutation.ipynb` | Permutation importance (domain and question level) |

## Methods summary

- **Cohort.** *All of Us* participants across 6-, 12-, and 24-month lookback
  windows relative to the index date. 
- **Features.** EHR (lab, condition, drug, measurement, observation,demographics),
  and *All of Us* survey responses. EHR concepts are filtered by
  minimum participant support and screened for label leakage.
- **Models.** Tabular: Logistic Regression, Random Forest, XGBoost, LightGBM,
  MLP. Sequential (two-tower): GRU, LSTM, Transformer — a sequential EHR
  encoder combined with a static branch (survey, demographics, summary
  statistics).
- **Evaluation.** PR-AUC as the primary metric; ROC-AUC and recall at fixed
  precision as secondary. Stratified train / validation / test split (68 / 12 / 20).


## Results

24-month PR-AUC, EHR vs. EHR+Survey (primary window):

| Model | EHR | EHR+Survey | Δ |
|---|---|---|---|
| Logistic Regression | 0.3672 | 0.4176 | +0.0505 |
| Random Forest | 0.5416 | 0.5851 | +0.0436 |
| XGBoost | 0.6226 | 0.6576 | +0.0350 |
| LightGBM | 0.6219 | **0.6603** | +0.0384 |
| MLP | 0.5106 | 0.5291 | +0.0185 |
| LSTM | 0.5683 | 0.5863 | +0.0181 |
| GRU | 0.5583 | 0.5967 | +0.0384 |
| Transformer | 0.5587 | 0.5914 | +0.0326 |

Survey features helped in all 24 model–window combinations (+0.0087 to +0.0505),
with the largest gains at 24 months, and ranked as the 2nd most important
information domain at 24 months. Survey availability differed by OUD status
(24-month coverage: 21.7% OUD-positive vs. 60.7% OUD-negative).

## Citation

Manuscript under review (AMIA 2026). Citation details will be added upon
acceptance.

## Acknowledgments

Supported by PCORI (Contract No. ME-2023C3-35532) and uses data from the
*All of Us* Research Program (NIH).
