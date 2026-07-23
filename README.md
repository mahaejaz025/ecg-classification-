# ecg-classification-
Deep learning models for automated ECG arrhythmia classification following the AAMI  5-class scheme (Normal, Supraventricular, Ventricular, Fusion, Unknown), with a focus  on explainability and cross-database generalization.
# ECG Arrhythmia Classification & Explainability

## Overview

Two 1D-CNN architectures — a multi-scale convolutional network (MultiScaleECGCNN) and 
a squeeze-excitation ResNet (ECGResNetSE) — are trained on the MIT-BIH Arrhythmia 
Database (via the Kachuee et al. preprocessed beat dataset) and evaluated both 
in-domain (held-out MIT-BIH test set) and zero-shot on an independent external 
database (INCART) to assess real-world generalization.

## Key results

| Model            | MIT-BIH Test Acc | MIT-BIH Test Macro F1 | INCART (zero-shot) Acc | INCART Macro F1 |
|------------------|:---:|:---:|:---:|:---:|
| MultiScaleECGCNN | 0.982 | 0.892 | 0.826 | 0.285 |
| ECGResNetSE      | 0.987 | 0.923 | 0.871 | 0.348 |

External validation on INCART shows a meaningful drop under domain shift, with 
ventricular ectopic beat (PVC) recall remaining reasonably robust (0.70–0.74) while 
rarer classes (Fusion, Unknown) are underrepresented in INCART (n=219, n=6) and 
should be interpreted with caution.

## Repository contents

- `training/` — model architectures and training scripts
- `evaluation/` — held-out test evaluation, external validation on INCART
- `preprocessing/` — beat extraction and windowing utilities
- `results/` — confusion matrices, classification reports, saved metrics (JSON)

## Data

- **Training/test**: [MIT-BIH Arrhythmia Database](https://physionet.org/content/mitdb/) 
  (Kachuee et al. preprocessed CSV, via Kaggle)
- **External validation**: [St Petersburg INCART 12-lead Arrhythmia Database](https://physionet.org/content/incartdb/)
