# Cyclone Rapid Intensification Prediction

Machine learning prediction of **24-hour rapid intensification (RI)** in North Indian Ocean cyclones using IMD best-track data.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/manojperi26/cyclone-rapid-intensification/blob/main/notebooks/Cyclone_RI_Predictor.ipynb)

## Problem
Given a cyclone's position, intensity and recent history at a fix, will its maximum sustained wind increase by **at least 30 kt within the next 24 hours**?

## Dataset
- IMD RSMC New Delhi best-track record, parsed with the `imdtrack` library
- 425 storms, 7,583 fixes (1982-2026)
- Basins: Bay of Bengal, Arabian Sea, Land

## Method
- **Label:** RI = 1 if wind(t + 24h) - wind(t) >= 30 kt, matched on the exact 24h future of the same storm
- **Features (10):** wind, central pressure, pressure drop, Dvorak CI number, latitude, longitude, month, basin, grade, past 24h wind change
- **Split:** storm-wise `GroupShuffleSplit` (25% test, seed 42) to avoid leakage between neighbouring fixes of one storm
- **Preprocessing:** median imputation + standardisation, fitted on train only
- **Imbalance:** balanced class weights (RI is only 7.85% of the test set)
- **Models:** Logistic Regression, Decision Tree, Random Forest, Gradient Boosting, SVM (RBF)

## Results
Test set: 1,134 fixes, 89 RI cases (RI class metrics)

| Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|
| Logistic Regression | 0.647 | 0.144 | 0.708 | 0.240 |
| Decision Tree | 0.626 | 0.155 | **0.843** | 0.261 |
| Random Forest | 0.787 | 0.222 | 0.685 | **0.335** |
| Gradient Boosting | **0.821** | **0.236** | 0.573 | 0.334 |
| SVM (RBF) | 0.694 | 0.164 | 0.708 | 0.266 |

Track and intensity history carry a measurable but limited signal for RI. All models have low precision (many false alarms).

## Limitations
- No environmental predictors (SST, ocean heat content, wind shear)
- Only 89 positive test cases, strongly correlated within storms
- Central pressure missing for many older fixes (median imputed)
- Best-track data quality is lower in older years

## Future work
- Add SST, ocean heat content and wind shear from reanalysis data
- Threshold tuning with precision-recall analysis
- Repeated grouped cross-validation
- Chronological hold-out (train on early years, test on latest)

## Repository structure
```
notebooks/   Cyclone_RI_Predictor.ipynb
report/      Research paper (PDF)
requirements.txt
```

## Run
```bash
git clone https://github.com/manojperi26/cyclone-rapid-intensification.git
cd cyclone-rapid-intensification
pip install -r requirements.txt
jupyter notebook notebooks/Cyclone_RI_Predictor.ipynb
```

## Author
**Peri Naga Venkata Sai Manoj**
B.Tech CSE (AI & Data Science), Lovely Professional University
[GitHub](https://github.com/manojperi26) | [LinkedIn](https://linkedin.com/in/manojperi26)

## Reference
Data: India Meteorological Department, RSMC New Delhi. Parsing: H. A. Syed, *imdtrack*, 2026, doi:10.5281/zenodo.21301659.
