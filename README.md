# Five-year mortality prediction in endometrial carcinoma (TCGA-UCEC)

Capstone project for the MBA in Artificial Intelligence and Big Data, University of São Paulo (ICMC-USP).

> The thesis is written in Portuguese (PDF in `docs/`). Code comments and figure labels are also in Portuguese.

## Overview

A machine learning pipeline that predicts death within five years from tumor gene expression (TCGA-UCEC). It focuses on two biases that are common in the literature:

- **Censoring:** living patients with less than 60 months of follow-up are excluded, because their 5-year status is unknown. This takes the cohort from 522 to 183 patients (79 deaths).
- **Data leakage:** SMOTE runs inside the cross-validation pipeline, variance-based gene selection uses the training set only, and all models share a single train/test split.

## Key results

| Model | AUC-ROC (test set, n=55) |
|---|---|
| LASSO (full transcriptome) | 0.690 |
| Linear SVM | 0.664 |
| RF, top 1,000 genes | 0.649 |
| RF, clinical only | 0.608 |

- LASSO had the best point AUC and selected 43 genes with non-zero coefficients, including MACC1, POSTN and PSMB9.
- McNemar tests and bootstrap confidence intervals (1,000 resamples) showed no significant difference between most models. The exception was the senescence signature, which was worse than LASSO (bootstrap p = 0.044).
- A controlled analysis compared AWST normalization with log2(TPM+1) on 9,432 genes mapped from HGNC symbols to Ensembl IDs.

## Repository structure

```
notebooks/   main notebook (runs on Colab or locally)
data/        input data (not versioned)
figures/     generated figures
docs/        thesis PDF (Portuguese)
```

## Data

Data are not included in this repository. To run the notebook, place these files in `data/` (or in `MyDrive/MBA_Dados/` on Colab):

| File | Content | Source |
|---|---|---|
| `exprData.pkl` | AWST expression, 522 patients x 10,164 genes | TCGA-UCEC (GDC), AWST preprocessing |
| `clin_f.pkl` | Clinical data (AGE, RADIATION_THERAPY, SUBTYPE, OS_STATUS, OS_MONTHS) | cBioPortal |
| `Data_Sheet_1.CSV` | Senescence gene list | Supplementary material, Wei et al. (2024) |
| TPM matrix | log2(TPM+1) expression, Ensembl IDs | GDC |
| `mba_gse106191_*.csv` | GSE106191 cohort (appendix) | GEO |

## How to run

```bash
pip install -r requirements.txt
jupyter notebook notebooks/tcc_mortalidade_5anos_ucec.ipynb
```

You can also open the notebook directly in Google Colab. The first cell detects the environment and sets the data path. The random seed is fixed at 42.

## Stack

Python, pandas, NumPy, scikit-learn, imbalanced-learn, XGBoost, statsmodels, mygene, matplotlib.

## Author

Francisco Martins Degiovani. MSc in Bioinformatics (FMRP-USP), MBA in AI and Big Data (ICMC-USP).
