# ECG Arrhythmia Classification & Explainability

Deep learning models for automated ECG arrhythmia classification following the AAMI
5-class scheme (Normal, Supraventricular, Ventricular, Fusion, Unknown), combining
1D signal-based and 2D image-based approaches with model explainability.

## Overview

This repository explores ECG arrhythmia classification via two complementary
approaches, each with model explainability:

1. **1D signal-based**: multi-scale and squeeze-excitation ResNet CNNs
   (`MultiScaleECGCNN`, `ECGResNetSE`) trained on MIT-BIH beat-level signals,
   evaluated in-domain (held-out MIT-BIH test set) and externally on the INCART
   database (zero-shot, no fine-tuning) to assess real-world generalization.
   Explained using SHAP, Grad-CAM, and LIME.
2. **2D image-based**: CNN trained on ECG image representations from the
   [ECG Images dataset of Cardiac Patients](https://data.mendeley.com/datasets/gwbz3fsgp8/2)
   (Mendeley), with LIME used to explain individual predictions.

## Key results — 1D models

| Model            | MIT-BIH Test Acc | MIT-BIH Test Macro F1 | INCART (zero-shot) Acc | INCART Macro F1 |
|------------------|:---:|:---:|:---:|:---:|
| MultiScaleECGCNN | 0.982 | 0.892 | 0.826 | 0.285 |
| ECGResNetSE      | 0.987 | 0.923 | 0.871 | 0.348 |

External validation on INCART shows a meaningful drop under domain shift.
Ventricular ectopic beat (PVC) recall remains reasonably robust (0.70–0.74) under
zero-shot transfer, while Fusion and Unknown classes are underrepresented in
INCART (n=219 and n=6 respectively) and their per-class metrics should be
interpreted with caution rather than treated as reliable estimates.

**Note on preprocessing**: beat windowing for the INCART evaluation was
reconstructed from the published Kachuee et al. (2018) preprocessing convention,
as the original training-time preprocessing source was unavailable. This was
validated by visual comparison against MIT-BIH beats; agreement was strong for
Normal and Supraventricular classes, with residual uncertainty for
Ventricular/Fusion likely related to local heart-rate clustering around ectopic
runs in INCART. See `preprocessing/` for details.

## Explainability

- **1D models** (`MultiScaleECGCNN`, `ECGResNetSE`):
  - **SHAP** (SHapley Additive exPlanations) — per-sample feature attribution
    across the 187-sample beat signal.
  - **Grad-CAM** — class-discriminative localization over convolutional feature
    maps, adapted for 1D signal input.
  - **LIME** (Local Interpretable Model-agnostic Explanations) — local
    surrogate-model explanations for individual beat classifications.
- **2D model**:
  - **LIME** — highlights which regions of an ECG image most influenced a given
    classification.

## Data

- **1D signal-based training/test**: [MIT-BIH Arrhythmia Database](https://physionet.org/content/mitdb/)
  (Kachuee et al. preprocessed CSV, via Kaggle: `shayanfazeli/heartbeat`)
- **1D external validation**: [St Petersburg INCART 12-lead Arrhythmia Database](https://physionet.org/content/incartdb/)
  (PhysioNet `incartdb`, 75 records, beat-level AAMI-compatible annotations)
- **2D representation**: [ECG Images dataset of Cardiac Patients](https://data.mendeley.com/datasets/gwbz3fsgp8/2)
  (Khan, A. H. & Hussain, M., Ch. Pervaiz Elahi Institute of Cardiology, Multan,
  Pakistan. Published 19 March 2021, Version 2. DOI: [10.17632/gwbz3fsgp8.2](https://doi.org/10.17632/gwbz3fsgp8.2)),
  used for image-based (2D CNN) classification as a complementary approach to the
  1D signal-based models above.

## Models

### 1D — MultiScaleECGCNN
Multi-scale 1D convolutional network with three parallel receptive-field paths
(kernel sizes 3/9/21), squeeze-excitation fusion, and DropPath regularization.
~131K parameters.

### 1D — ECGResNetSE
1D ResNet with squeeze-excitation blocks. ~424K parameters.

### 2D — MendeleyResNet
ResNet-based 2D CNN trained on ECG images from the Mendeley Cardiac Patients
dataset.
<Add further architecture details once finalized — input image size, backbone
depth, augmentation strategy, number of parameters.>

## Acknowledgments

- MIT-BIH Arrhythmia Database and preprocessing convention: Kachuee, M., Fazeli,
  S., & Sarrafzadeh, M. (2018). ECG Heartbeat Classification: A Deep Transferable
  Representation. *IEEE ICHI*.
- St Petersburg INCART 12-lead Arrhythmia Database, distributed via PhysioNet.
- Khan, A. H., & Hussain, M. (2021). ECG Images dataset of Cardiac Patients
  (Version 2). Mendeley Data. https://doi.org/10.17632/gwbz3fsgp8.2
