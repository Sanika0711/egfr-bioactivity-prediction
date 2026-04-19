# EGFR Bioactivity Prediction Using Machine Learning

## Overview
This project builds a machine learning pipeline to predict the bioactivity of compounds against **EGFR (Epidermal Growth Factor Receptor)** — a key drug target in cancer therapy, particularly in non-small cell lung cancer (NSCLC) and breast cancer.

Compound data was sourced from **BindingDB**, and bioactivity was measured using IC50 values converted to pIC50 for modelling.

---

## Background
EGFR is a receptor tyrosine kinase that, when mutated or overexpressed, drives uncontrolled cell proliferation. Approved EGFR inhibitors include gefitinib, erlotinib, and osimertinib. Predicting the bioactivity of new compounds against EGFR computationally can accelerate early-stage drug discovery by prioritising candidates before expensive wet-lab testing.

---

## Pipeline Summary

```
BindingDB TSV Data
       ↓
Data Cleaning & IC50 Filtering
       ↓
IC50 → pIC50 Conversion
       ↓
Lipinski Descriptor Calculation (RDKit)
       ↓
Exploratory Data Analysis (Seaborn)
       ↓
Mordred Molecular Descriptor Generation
       ↓
Feature Selection (VarianceThreshold)
       ↓
Random Forest Regressor
       ↓
Model Evaluation (R², MAE, RMSE, Cross-Validation)
```

---

## Key Steps

### 1. Data Preprocessing
- Loaded EGFR compound data from BindingDB
- Dropped rows with missing IC50 values
- Converted IC50 (nM) to pIC50 using: `pIC50 = -log10(IC50 × 10⁻⁹)`
- Capped extreme IC50 values at 100,000,000 nM to avoid negative pIC50

### 2. Lipinski's Rule of Five
Calculated four physicochemical descriptors using RDKit:
- Molecular Weight (MW)
- LogP (lipophilicity)
- Number of H-bond Donors
- Number of H-bond Acceptors

Applied **Mann-Whitney U test** to assess statistical significance of descriptor differences between activity classes.

### 3. Mordred Descriptors
Generated ~1,600 molecular descriptors using the Mordred library for use as ML features. Applied variance thresholding to remove low-information features.

### 4. Random Forest Model
- Algorithm: `RandomForestRegressor` (scikit-learn)
- Target variable: pIC50 (continuous, regression task)
- Train/test split: 80/20

---

## Results

| Metric | Value |
|--------|-------|
| R² Score | 0.618 |
| Cross-validated R² | 0.567 |
| MAE | 0.114 |
| RMSE | 0.172 |

**Dataset:** 240 compounds, EGFR target, BindingDB  
**Model:** Random Forest Regressor (200 trees, max_depth=8)  
**Features:** 1,189 Mordred descriptors (variance filtered)  
**Split:** 80% train / 20% test  

The cross-validated R² of 0.567 indicates the model explains approximately 
57% of variance in pIC50 values — a reasonable baseline given the dataset 
size constraints outlined below.

---

## Limitations & Future Work
The dataset contains 240 compounds after preprocessing, which is relatively 
small for the number of Mordred descriptors generated (1,189). This high 
feature-to-sample ratio limits model performance.

Future improvements:
- Expand dataset by including IC50 data from ChEMBL once API is restored 
  (target: 1,000+ compounds)
- Incorporate 3D conformer-based descriptors for richer molecular representation
- Test gradient boosting models (XGBoost, LightGBM)
- Apply cross-validated hyperparameter tuning (GridSearchCV)

---

## EDA Highlights
- MW and LogP showed statistically significant differences between high and low activity classes (p < 0.05)
- NumHDonors showed no significant difference between classes
- The dataset is biased toward high-potency compounds (pIC50 > 10), so percentile-based activity classification was used

---

## Technologies Used
- Python 3.x
- pandas, numpy
- RDKit
- Mordred
- scikit-learn
- seaborn, matplotlib
- scipy

---

## How to Run

```bash
# 1. Clone the repo
git clone https://github.com/Sanika0711/egfr-bioactivity-prediction.git
cd egfr-bioactivity-prediction

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your BindingDB EGFR dataset as 'EGFR Targets.tsv'

# 4. Open and run the notebook
jupyter notebook EGFR Bioactivity Prediction Using Machine Learning.ipynb
```

---

## Requirements
```
pandas
numpy
rdkit
mordred
scikit-learn
seaborn
matplotlib
scipy
```

---

## Author
**Sanika Medhekar**  
MSc Drug Discovery & Pharma Management — University College London  
B.Pharmacy — University of Mumbai  

[LinkedIn](https://www.linkedin.com/in/sanika-medhekar) | [GitHub](https://github.com/Sanika0711)

---

## Acknowledgements
- Compound bioactivity data: [BindingDB](https://www.bindingdb.org/)
- Molecular descriptors: [RDKit](https://www.rdkit.org/) and [Mordred](https://github.com/mordred-descriptor/mordred)
- - Project inspired by the [Bioinformatics Project from scratch](https://github.com/dataprofessor/bioinformatics) 
  tutorial series by [Chanin Nantasenamat (Data Professor)](https://github.com/dataprofessor)
  
