# Model Architecture

This document outlines the machine learning architecture used for detecting malicious or anomalous network traffic.

---

## 🎯 Objective

To build a hybrid detection pipeline combining:

* **Unsupervised anomaly detection** using Isolation Forest
* **Supervised classification** using Random Forest (cuML on GPU)

---

## 🧩 Model 1: Isolation Forest

* **Library**: Scikit-learn
* **Purpose**: Early-stage anomaly detection without labeled data
* **Parameters**:

  ```python
  IsolationForest(
      contamination=0.05,
      random_state=42,
      n_jobs=-1
  )
  ```
* **Output**: `-1` = malicious, `1` = benign

---

## 🧩 Model 2: Random Forest (cuML)

* **Library**: RAPIDS cuML
* **Purpose**: High-speed classification on balanced, labeled data
* **Parameters**:

  ```python
  RandomForestClassifier(
      n_estimators=2000,
      max_depth=150,
      min_samples_split=5,
      random_state=42,
      n_streams=1
  )
  ```
* **Input**: Balanced dataset using SMOTE
* **Threshold**: `rf_proba[1] > 0.06` → anomaly

---

## ⚖️ Balancing Strategy

* **Library**: imbalanced-learn (SMOTE)
* **Why**: Network datasets are heavily imbalanced
* **Approach**: Oversample minority class (attack data)

---

## 🧠 Inference Logic

1. Scale input using `StandardScaler`
2. Run Isolation Forest → `malicious` or `benign`
3. Run Random Forest on GPU
4. If `rf_proba[1] > 0.06`: classify as `anomaly`
5. Return confidence + results from both models

---

Both models complement each other to improve reliability and reduce false positives.
