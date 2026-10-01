# Phase 3 Report — Supervised Model Comparison

**Project:** Indian Stock Market Analysis — Predicting Financial Health & Discovering Market Segments  
**Course:** Machine Learning Course Project  
**Student:** [Aapka naam yahan]  
**Date:** [Aaj ki date]  
**Dataset:** Indian Stock Market Dataset (Kaggle) — 5,258 rows, 8 columns  
**Editor:** VS Code + Python 3.x + Jupyter extension

---

## Executive Summary

Phase 3 mein humne **4 supervised classification models** train kiye aur compare kiye — KNN, SVM, Naive Bayes, aur Decision Tree. Sabhi models same preprocessed data (Phase 2 ka scaled output) pe train hue, aur same test set (1024 samples) pe evaluate kiye gaye.

**Primary metric:** F1 Score (kyunki class distribution 68/32 hai, accuracy alone misleading ho sakti hai)

**Winner:** **Decision Tree (max_depth=5)** — F1 = **0.5835**, Accuracy = **0.7588**

**Baseline (DummyClassifier):** Accuracy 0.6768, F1 0.0000 — sabhi models ne isse better perform kiya.

---

## 1. Methodology

### 1.1 Dataset State (from Phase 2)

- Train: 4094 samples × 5 features (scaled)
- Test: 1024 samples × 5 features (scaled)
- Target distribution: 0 → 67.73%, 1 → 32.27%
- Scaler: `StandardScaler`, fit on train only

### 1.2 Evaluation Metrics

- **Accuracy** — overall correct predictions
- **Precision** — kitne predicted "High ROCE" actually High ROCE the
- **Recall** — kitne actual "High ROCE" pakde gaye
- **F1 Score** — Precision + Recall ka harmonic mean (primary metric)

### 1.3 Reproducibility

- `random_state=42` har model mein
- SVM mein `class_weight='balanced'` (imbalance handling)
- DT mein `max_depth=5` (overfitting fix)

---

## 2. Model-by-Model Results

### 2.1 KNN (k=5)

Accuracy : 0.7373
Precision: 0.6000
Recall : 0.5619
F1 Score : 0.5803

text

- Confusion Matrix: `[[569, 124], [145, 186]]`
- Distance-based algorithm → scaled data mandatory
- k=5 standard starting value

### 2.2 SVM (RBF, class_weight='balanced')

Default SVM (no balancing):
F1 = 0.0464 ← minority class collapse

Balanced SVM:
Accuracy : 0.6963
Precision: 0.5420
Recall : 0.3897
F1 Score : 0.4534

text

- **Key learning:** Default SVM ne minority class ko ignore kar diya (recall 0.0242)
- `class_weight='balanced'` ne recall 0.02 se 0.39 kiya — 20x improvement
- Ye imbalanced classification ka textbook example hai

### 2.3 Naive Bayes (GaussianNB)

Accuracy : 0.3818
Precision: 0.3417
Recall : 0.9849
F1 Score : 0.5074

text

- **Sabse interesting case** — Recall 0.98 (almost saari class 1 pakdi)
- But Precision 0.34 (bahut false positives)
- Accuracy 0.38 — misleadingly low
- **Lesson:** Naive independence assumption financial data pe over-predict karta hai

### 2.4 Decision Tree (default vs tuned)

Default (depth 24, 736 leaves):
Accuracy : 0.7119
F1 Score : 0.5655
→ Overfit: har leaf pe ~5 train samples

Tuned (max_depth=5, 30 leaves):
Accuracy : 0.7588
Precision: 0.6603
Recall : 0.5227
F1 Score : 0.5835 ← WINNER

text

- **Overfitting demonstration:** Depth 24 → 736 leaves → F1 0.5655
- **Fix:** max_depth=5 → 30 leaves → F1 0.5835 (+0.018)
- Simple tree, better generalization

---

## 3. Final Comparison Table

| Rank | Model                      | Accuracy | Precision | Recall | F1     |
| ---- | -------------------------- | -------- | --------- | ------ | ------ |
| 1    | Decision Tree (depth=5)    | 0.7588   | 0.6603    | 0.5227 | 0.5835 |
| 2    | KNN (k=5)                  | 0.7373   | 0.6000    | 0.5619 | 0.5803 |
| 3    | Naive Bayes                | 0.3818   | 0.3417    | 0.9849 | 0.5074 |
| 4    | SVM (RBF, balanced)        | 0.6963   | 0.5420    | 0.3897 | 0.4534 |
| —    | DummyClassifier (baseline) | 0.6768   | 0.0000    | 0.0000 | 0.0000 |

**Bar chart:** `visualizations/model_comparison.png`

---

## 4. Best Model Justification

**Decision Tree (max_depth=5)** chuna gaya, kyunki:

1. **Highest F1 (0.5835)** — primary metric ke hisaab se best
2. **Highest Accuracy (0.7588)** — baseline se +8.2 percentage points
3. **Interpretable** — if-else rules se samjha ja sakta hai, viva mein defend karna easy
4. **Fast** — instant training aur prediction
5. **Balanced Precision/Recall** — KNN aur NB se zyada balanced

**Runner-up:** KNN (F1 = 0.5803) — sirf 0.003 ka gap. Viva mein mention kar sakte ho.

---

## 5. Key Learnings

### 5.1 Accuracy is not enough

- NB: Accuracy 0.38, F1 0.51
- SVM: Accuracy 0.70, F1 0.45
- **Lesson:** Imbalanced data pe accuracy misleading hoti hai

### 5.2 Class imbalance handling matters

- SVM default → F1 0.0464 (collapse)
- SVM balanced → F1 0.4534 (10x better)
- **Lesson:** `class_weight='balanced'` standard imbalanced-classification practice hai

### 5.3 Overfitting is real

- DT default (depth 24) → F1 0.5655
- DT tuned (depth 5) → F1 0.5835
- **Lesson:** Simple models often beat complex ones on test data

### 5.4 Scaling matters for some models

- KNN, SVM → scaling mandatory (distance-based)
- DT, NB → scaling optional
- Sab models same scaled data pe chalaye consistency ke liye

---

## 6. Limitations (Phase 3)

- **5 features only** — koi sector/industry info nahi, koi temporal data nahi
- **No cross-validation** — single 80/20 split use kiya (simplicity ke liye)
- **No hyperparameter tuning** — GridSearchCV avoid kiya (scope rule)
- **Static threshold** — ROCE > 15% ek fixed benchmark, industry-wise vary karta hai
- **F1 ceiling ~0.58** — features limited hain, accuracy cap hai

---

## 7. Deliverables

- [x] 4 supervised models trained (KNN, SVM, NB, DT)
- [x] Tuned DT (max_depth=5)
- [x] Comparison table (Section 3)
- [x] Bar chart saved (`visualizations/model_comparison.png`)
- [x] Best model justified
- [x] Notebook markdown cell (Cell 24) with observations

---

## 8. Next Phase — Phase 4 (Clustering)

**Goal:** K-Means clustering se natural company segments discover karna.

**Approach:**

1. Same 5 scaled features use karenge
2. Elbow method (k = 2 to 10) → optimal k choose
3. KMeans fit + cluster labels
4. 2D visualization (PCA ya top-2 features)
5. Cluster mean table + business interpretation

**Expected outcome:** 3–5 natural clusters (e.g., "high-growth large cap", "value stocks", "distressed small caps") — jo supervised labels se alag insight denge.

---

**Educational disclaimer:** Ye project academic analysis hai. Real stock prediction ya investment advice ke liye use nahi karna chahiye.
