# Identifying Key Predictors of Student Dropout Using Machine Learning

> A research project applying Random Forest classification to the UCI Student Dropout and Academic Success dataset, achieving **91.18% accuracy** — surpassing the primary benchmark study by **12.18 percentage points**.

---

## 📌 Overview

Student dropout is one of the most persistent and costly challenges in higher education — for individuals, institutions, and economies alike. This project was developed as part of an academic research paper investigating whether machine learning can be used to **identify the key predictors of student dropout early enough to enable proactive intervention**.

The goal was not simply to build a classifier, but to answer a more targeted question:

> *Which student features most strongly signal dropout risk, and can a leaner, more interpretable model outperform prior work?*

The answer, as demonstrated by the results, is yes.

---

## 🎓 Academic Context

This project is the implementation behind the paper:

**"Identifying Key Predictors of Student Dropout Using Machine Learning Techniques"**

The primary reference benchmark is:

> Martins, M.V., Tolledo, D., Machado, J., Baptista, L.M.T. and Realinho, V. (2021). *Early Prediction of Student's Performance in Higher Education: A Case Study*. Advances in Intelligent Systems and Computing, 1365, pp. 166–175. https://doi.org/10.1007/978-3-030-72657-7_16

That study achieved **79% accuracy** using all 33 features under a three-class formulation (Dropout, Enrolled, Graduate). This project surpasses that benchmark by reducing the feature space, engineering new features, and reframing the problem as binary classification.

| | Martins et al. (2021) | This Project |
|---|---|---|
| Dataset | UCI Portalegre | UCI Portalegre |
| Features used | 33 (all) | 28 (selected + engineered) |
| Classification type | Three-class | Binary |
| Best accuracy | 79% | **91.18%** |
| Feature importance | Not conducted | ✅ Gini importance |
| Hyperparameter tuning | Not reported | ✅ Optuna (100 trials) |

---

## 📂 Repository Structure

```
├── data/
│   ├── data.csv                        # Original UCI dataset (semicolon-separated)
│   ├── data(Some features dropped).csv # After MI-based feature selection (25 features)
│   ├── data(Some features added).csv   # After feature engineering (28 features)
│   └── Binary_data.csv                 # Final binary dataset (Dropout vs Graduate)
│
├── eda.ipynb                           # Exploratory Data Analysis
├── Feature_Engineering.ipynb          # Feature selection and engineering pipeline
├── model.ipynb                         # Model training, evaluation, and optimisation
│
└── README.md
```

---

## 🔬 Methodology

The project followed an iterative four-stage pipeline:

### Stage 1 — Baseline (33 features, three-class)
Both KNN (K=3) and Random Forest were trained on the raw dataset with all 33 features and the original three-class target: Dropout, Enrolled, Graduate.

| Model | Accuracy |
|---|---|
| KNN (K=3) | 59.89% |
| Random Forest | 76.38% |

### Stage 2 — Feature Selection (25 features)
Mutual Information (MI) scores were computed for all 33 features. Features with MI < 0.02 were removed as they contributed noise rather than signal — reducing the feature set to **25 features**.

> Random Forest accuracy after selection: **77.06%**

### Stage 3 — Feature Engineering (28 features)
Three domain-informed composite features were synthesised:

| Feature | Formula | What it captures |
|---|---|---|
| `Grade_Trend` | `CU 2nd sem (grade) − CU 1st sem (grade)` | Academic trajectory across semesters |
| `Approval_Rate_2nd` | `CU 2nd sem (approved) ÷ CU 2nd sem (enrolled)` | Fraction of enrolled units actually passed |
| `Financial_Risk_Score` | `Debtor + (1 − Tuition fees up to date) − Scholarship holder` | Composite financial vulnerability |

> Random Forest accuracy after engineering: **77.40%**

### Stage 4 — Binary Classification (Dropout vs Graduate)
The "Enrolled" class was removed. Students classified as Enrolled had no resolved academic outcome, making them ambiguous targets that artificially suppressed classifier performance. Reframing as binary produced the most significant accuracy jump.

> Random Forest accuracy (binary): **91.18%** ✅

---

## ⚙️ Hyperparameter Optimisation

[Optuna](https://optuna.org/) was used to search for the optimal Random Forest hyperparameters across 100 trials using the Tree-structured Parzen Estimator (TPE) algorithm.

**Optimal parameters found:**

| Parameter | Value |
|---|---|
| `n_estimators` | 235 |
| `max_depth` | 10 |
| `min_samples_split` | 10 |
| `min_samples_leaf` | 3 |
| `max_features` | sqrt (≈ 5 features per split) |

---

## 📊 Top 10 Most Predictive Features

Ranked by Gini importance from the final model:

1. **Approval Rate (2nd Semester)** ← engineered feature
2. Curricular units 2nd sem (approved)
3. Curricular units 2nd sem (grade)
4. Curricular units 1st sem (approved)
5. Curricular units 1st sem (grade)
6. Tuition fees up to date
7. **Financial Risk Score** ← engineered feature
8. Admission grade
9. **Grade Trend** ← engineered feature
10. Age at enrollment

All three engineered features appeared in the top 10 — validating the feature engineering step.

---

## 📈 Final Model Performance

**Accuracy: 91.18%**

| Class | Precision | Recall | F1-Score | Support |
|---|---|---|---|---|
| Dropout | 0.91 | 0.84 | 0.87 | 277 |
| Graduate | 0.90 | 0.95 | 0.93 | 449 |
| **Weighted avg** | **0.91** | **0.91** | **0.91** | **726** |

---

## 🗃️ Dataset

**UCI Predict Students' Dropout and Academic Success**
- Source: [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/697/predict+students+dropout+and+academic+success)
- Records: 4,424 students
- Original features: 35 input features + 1 target
- No missing values
- Originally compiled by Realinho, Machado, Baptista & Martins (2022), Polytechnic Institute of Portalegre, Portugal

> ⚠️ The dataset is not included in this repository. Download it from the UCI link above and place `data.csv` in the `data/` folder before running the notebooks.

---

## 🚀 Getting Started

### Requirements
```bash
pip install pandas scikit-learn seaborn matplotlib optuna
```

### Run order
```
1. eda.ipynb               — Explore the raw dataset
2. Feature_Engineering.ipynb — Feature selection and engineering (generates CSV files in data/)
3. model.ipynb             — Model training, evaluation, and Optuna optimisation
```

---

## 📖 References

1. Martins, M.V., Tolledo, D., Machado, J., Baptista, L.M.T. and Realinho, V. (2021). *Early Prediction of Student's Performance in Higher Education: A Case Study*. Advances in Intelligent Systems and Computing, 1365, pp. 166–175. https://doi.org/10.1007/978-3-030-72657-7_16

2. Realinho, V., Machado, J., Baptista, L. and Martins, M.V. (2022). *Predicting Student Dropout and Academic Success*. Data, 7(11), p. 146. https://doi.org/10.3390/data7110146

3. Breiman, L. (2001). *Random Forests*. Machine Learning, 45(1), pp. 5–32. https://doi.org/10.1023/A:1010933404324

---

## 📝 License

This project is for academic and research purposes.
