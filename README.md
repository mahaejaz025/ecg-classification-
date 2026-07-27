# Explainable AI for Automated ECG Arrhythmia Classification

Code accompanying the paper *"Explainable Artificial Intelligence for Automated ECG Arrhythmia
Classification."*

A compact multi-scale 1D CNN (**MultiScaleECGCNN**, ~131.6K parameters) for five-class AAMI
heartbeat classification, paired with a quantitatively audited interpretability layer. Explanations
from SHAP, Grad-CAM and an adaptive class-tuned LIME are scored by the fraction of attribution mass
falling inside the QRS complex, so every prediction carries a per-beat trust score rather than a
picture.

## Key results

| | |
|---|---|
| MIT-BIH held-out accuracy | 98.18% (21,892 beats) |
| Weighted F1 / macro F1 | 0.981 / 0.892 |
| Parameters / model size | 131,573 / 0.52 MB |
| Single-beat CPU latency | 2.675 ms |
| Zero-shot INCART accuracy | 87.8% (macro F1 0.341) |
| QRS trust: LIME-1D / LIME-2D / SHAP / Grad-CAM | 0.439 / 0.437 / 0.316 / 0.265 |

Transfer to INCART is class-dependent: morphology-defined classes (Normal, PVC) carry over, while
the rhythm-defined supraventricular and fusion classes do not. See the paper's external-validation
section for the analysis.

## Repository contents

| File | Contents | Reproduces |
|---|---|---|
| `ecg_datasets_and_models.py` | Dataset loading, Butterworth filtering, segmentation, split-then-SMOTE, and the 1D/2D model definitions | Tables 1–3 |
| `ecg_ablation_study.py` | Component ablations across seeds | Ablation table, ablation figure |
| `5_fold_cv_+_mcnemar.py` | Five-fold stratified CV, McNemar's test, bootstrap CIs | Cross-validation and statistical tables |
| `ecg_xai_shap_and_gradcam.py` | SHAP and Grad-CAM attributions with QRS trust scoring | SHAP and Grad-CAM figures |
| `ecg_xai_lime_1d.py` | Adaptive class-tuned LIME-1D | LIME-1D figure, per-class trust table |
| `ecg_xai_lime_2d.py` | LIME-2D on the image branch | LIME-2D figure |
| `xai_faithfulness_and_stability.py` | AOPC, comprehensiveness, sufficiency, stability | Faithfulness table |
| `external_validation_incart.py` | Zero-shot INCART evaluation, no fine-tuning | External-validation table |

> Adjust the filenames and the "Reproduces" column to match your repo exactly — the table numbers
> above are placeholders keyed to the manuscript.

## Datasets

None of the datasets are redistributed here; each is downloaded from its original source.

| Dataset | Use | Source |
|---|---|---|
| ECG Heartbeat Categorization Dataset (Kaggle) | Pre-segmented MIT-BIH beats, primary experiments | Kaggle |
| MIT-BIH Arrhythmia Database | Raw 360 Hz recordings | https://physionet.org/content/mitdb/ |
| Mendeley 12-lead ECG images | Image branch | https://doi.org/10.17632/gwbz3fsgp8.2 |
| St Petersburg INCART | Zero-shot external validation only — never used for training, tuning or model selection | https://physionet.org/content/incartdb/ |

## Setup

```bash
git clone https://github.com/mahaejaz025/ecg-classification.git
cd ecg-classification
pip install -r requirements.txt
```

Python 3.10+ recommended. SHAP and LIME versions are pinned because their APIs have changed across
releases.

## Reproducing the results

Run in this order; each script assumes the previous one's outputs are present.

```bash
python ecg_datasets_and_models.py        # preprocess and train
python 5_fold_cv_+_mcnemar.py            # cross-validation and significance tests
python ecg_ablation_study.py             # component ablations
python ecg_xai_shap_and_gradcam.py       # SHAP and Grad-CAM attributions
python ecg_xai_lime_1d.py                # adaptive LIME-1D
python ecg_xai_lime_2d.py                # LIME-2D on the image branch
python xai_faithfulness_and_stability.py # faithfulness and stability metrics
python external_validation_incart.py     # zero-shot INCART evaluation
```

Trained weights are not distributed; all models retrain from this code under the reported
configuration. Random seeds are fixed and reported per model, so results should reproduce to within
the seed-to-seed variation documented in the paper.

## Requirements

See `requirements.txt`. Core dependencies: PyTorch, NumPy, SciPy, scikit-learn, imbalanced-learn
(SMOTE), wfdb, SHAP, LIME, Matplotlib.

## License

Code released under the MIT License — see `LICENSE`.

The datasets keep their own terms. MIT-BIH and INCART are distributed by PhysioNet under the Open
Data Commons Attribution License; the Mendeley image collection is under its own Mendeley Data
license. Cite the original sources if you use them.

```

Update with the journal, volume and DOI once published.
