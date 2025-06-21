# 🚨 Automated Threat Detection using ML in Python

**Completion Date:** April 2025  
**Platform:** Ubuntu 20.04.6 LTS  
**Type:** Real-time Anomaly Detection Web App using Machine Learning and Flask

---

## 🧠 Overview

This project uses machine learning to detect anomalous and potentially malicious network traffic. It combines:

- **Isolation Forest** (unsupervised anomaly detection)
- **Random Forest (cuML)** for high-accuracy classification on GPU
- **SMOTE** to balance the training dataset
- **Flask REST API** for backend inference
- **Responsive UI** built with HTML/CSS/JavaScript + Chart.js

---

## ⚙️ Model Details

### 📌 Isolation Forest
```python
IsolationForest(
    contamination=0.05,
    random_state=42,
    n_jobs=-1
)
Detects outliers from network traffic

Labels as malicious or benign

### 📌 Random Forest (GPU - RAPIDS cuML)
python
Copy
Edit
RandomForestClassifier(
    n_estimators=2000,
    max_depth=150,
    min_samples_split=5,
    random_state=42,
    n_streams=1
)
Trained on SMOTE-balanced data

Predicts anomaly or normal with confidence

Threshold for anomaly set at 0.06

### 📥 Input Features Used
Flow Duration

Total Fwd Packets / Total Backward Packets

Fwd Packet Length Mean / Bwd Packet Length Mean

Flow Bytes/s / Flow Packets/s

Fwd PSH Flags / Bwd PSH Flags

SYN Flag Count / ACK Flag Count / URG Flag Count

Packet Length Mean

## 🌐 Web App Demo
Users enter network flow features in a form

Results from both models are displayed in real time

Dynamic charts visualize confidence and prediction history

## 🧪 Sample Output
yaml
Copy
Edit
Isolation Forest: benign
Random Forest: anomaly
Confidence: 0.91
## 🔧 Tech Stack
Layer	Tool / Framework
Language	Python 3.10
Backend	Flask
Frontend	HTML, CSS, JS, Chart.js
ML Models	Scikit-learn, RAPIDS cuML
Balancing	imbalanced-learn (SMOTE)
GPU Accel	RAPIDS cuDF + cuML
Logging	Python logging module

## 🧱 Project Structure
bash
Copy
Edit
automated-threat-detection/
├── app/
│   └── atd.py                # Main Flask backend
├── models/
│   ├── rf_model.pkl
│   ├── iso_forest.pkl
│   └── scaler.pkl
│   
├── docs/
│   ├── model_architecture.md
│   ├── prediction_flow.md
│   └── tools_used.md
├── requirements.txt
├── README.md
## ▶️ How to Run Locally

# Create environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Run Flask app
python app/atd.py
Ensure GPU is set up for cuML, RAPIDS, and NVIDIA drivers.

## 📄 Documentation
docs/model_architecture.md: How both models are trained

docs/prediction_flow.md: Data → Prediction pipeline

docs/tools_used.md: Libraries, OS, and tools used

## 🧠 Skills Demonstrated
Machine learning model development

Imbalanced dataset handling (SMOTE)

GPU-accelerated ML with RAPIDS

REST API design (Flask)

Dashboard building (HTML, CSS, Chart.js)

Real-time prediction handling and error catching