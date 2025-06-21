# Prediction Flow

This document explains the step-by-step process followed by the app when a prediction request is received from the UI.

---

## 🧾 1. User Input (from Web Form)

* User enters 13 network flow feature values
* Fields include packet counts, durations, flags, byte/packet rates

---

## 🧹 2. Preprocessing

* Inputs are converted to `float32`
* Invalid/negative values are rejected
* Values are standardized using a pre-fitted `StandardScaler`

---

## 🔎 3. Isolation Forest Prediction

* Model runs on preprocessed data
* Returns binary output:

  * `-1` → `malicious`
  * `1` → `benign`

---

## 🌲 4. Random Forest Prediction (cuML)

* Scaled input converted to cuDF format
* Predicts class probabilities:

  * `rf_proba[1] > 0.06` → `anomaly`
  * Otherwise → `normal`

---

## 📈 5. Confidence Score

* Extracted from Random Forest’s prediction probabilities
* Displayed visually as a donut chart in the UI

---

## 🧾 6. JSON Response (to Frontend)

Example:

```json
{
  "isolation_forest_result": "benign",
  "random_forest_result": "anomaly",
  "confidence": 0.91
}
```

---

## 📊 7. Dashboard Updates

* Results displayed on screen
* Prediction history stored and shown in pie chart

This modular flow ensures high-performance, low-latency predictions with enhanced visibility for the user.
