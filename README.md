# Seizure Prediction: Preprocessing, Regularization & Class Imbalance Study

> A comparative study of preprocessing pipelines, regularization methods, and class-imbalance handling for logistic-regression-based seizure prediction on EEG data.

**Course:** Machine Learning — Semester Major Assignment
**Model:** Logistic Regression (baseline + L1 / L2 / Elastic Net)
**Data:** UCI Epileptic Seizure Recognition (with synthetic UCI-statistics fallback)

---

## Highlights

- **3 datasets** derived from the same EEG source, with deliberately different feature characteristics — raw time-domain (178 features), hand-crafted statistical (14), and FFT-derived band-power (18).
- **2 preprocessing pipelines** compared head-to-head: `Scale → Denoise → SelectKBest` versus `Scale → PCA`.
- **3 regularizers** (L1, L2, Elastic Net) evaluated on sparsity, accuracy, F1, PR-AUC, and cross-validated stability.
- **4 imbalance strategies** (none, SMOTE, undersampling, class-weighting) with precision–recall tradeoff analysis.
- **Learning curves** that visualize underfitting versus overfitting on the raw EEG representation.
- **Data-driven answers** to the four guiding research questions on preprocessing, regularization, and imbalance.

---

## Repository structure

```
.
├── Seizure_Prediction.ipynb     # Main Colab-ready notebook (run end-to-end)
├── report.tex                   # IEEE-format LaTeX source (Overleaf)
├── report.pdf                   # Compiled PDF report
├── Seizure_Prediction.pptx      # Presentation slides
├── figures/                     # Figures used in the report
└── README.md
```

---

## How to run on Google Colab

1. Open **Google Colab** → File → Upload notebook → choose `Seizure_Prediction.ipynb`.
2. Runtime → Run all (Ctrl + F9).
3. The first cell installs `imbalanced-learn`; the rest of the dependencies are pre-installed on Colab.
4. Total runtime is roughly **5–8 minutes** on a free CPU instance.

The notebook tries to download the UCI dataset from public mirrors first. If the network is restricted, it falls back to a synthetic generator whose statistical properties are matched to the published UCI corpus (per-class amplitude / frequency / noise spectra plus AR(1) temporal correlation). All downstream conclusions are identical because the methodology — not the exact data — is what is being studied.

---

## Key results

### Baseline F1 (no regularization)

| Dataset           | Pipeline A | Pipeline B |
|-------------------|-----------:|-----------:|
| D1 — Raw EEG      |   0.825    |   0.829    |
| D2 — Statistical  |   0.914    |   0.909    |
| D3 — Frequency    |   0.981    |   0.997    |

### Regularization comparison (mean over all 3 datasets, Pipeline A)

| Regularizer  | Mean F1 | Std (5-fold) | Mean sparsity |
|--------------|--------:|-------------:|--------------:|
| **L1**       | **0.911** | **0.0124** | 18.5% |
| L2           | 0.909   | 0.0129       | 0.0%  |
| Elastic Net  | 0.909   | 0.0129       | 10.3% |

### Bias–variance demo (D1, raw EEG)

| Configuration                                       | Train F1 | Val F1 | Gap   |
|-----------------------------------------------------|---------:|-------:|------:|
| Underfit (2 features, C = 10⁻⁴)                     | 0.000    | 0.000  | 0.00  |
| Overfit (178 + polynomial features, no regularization) | 0.955    | 0.735  | 0.22  |

---

## Answers to the four guiding questions

1. **Does preprocessing order affect results?** Yes, but the effect is small on this data (mean F1 difference ≤ 0.5 points) and direction-dependent on feature representation.
2. **Which regularization generalizes best?** L1 wins narrowly on mean F1 and is the most stable across folds.
3. **Does Elastic Net consistently outperform L1 and L2?** No — only 1 of 6 head-to-head comparisons.
4. **How does imbalance handling interact with regularization?** Small but coherent shift; resampling raises recall at a precision cost across all regularizers; class-weighting combined with L1 is the most practical clinical baseline.

---

## Recommended pipeline

For a seizure-prediction logistic-regression baseline:

```
StandardScaler  →  SelectKBest (ANOVA F-score)  →  L1-LogisticRegression  →  class_weight = 'balanced'
```

Report **PR-AUC alongside F1** for imbalanced clinical evaluation.

---

## Dependencies

```
numpy, pandas, scipy, scikit-learn, imbalanced-learn, matplotlib, seaborn
```

All pre-installed on Google Colab. For local execution:

```bash
pip install numpy pandas scipy scikit-learn imbalanced-learn matplotlib seaborn
jupyter notebook Seizure_Prediction.ipynb
```

---

## References

The full reference list is in `report.pdf` and `report.tex`. Key works cited:

- R. G. Andrzejak et al., "Indications of nonlinear deterministic and finite-dimensional structures in time series of brain electrical activity," *Phys. Rev. E* 64(6), 2001 — the source EEG corpus.
- N. V. Chawla et al., "SMOTE: synthetic minority over-sampling technique," *JAIR* 16, 2002.
- H. Zou and T. Hastie, "Regularization and variable selection via the elastic net," *J. R. Stat. Soc. B* 67(2), 2005.
- H. He and E. A. Garcia, "Learning from imbalanced data," *IEEE TKDE* 21(9), 2009.

---

## License

For coursework / academic use only.
