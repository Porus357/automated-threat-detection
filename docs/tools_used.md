# Tools and Technologies Used

This project leverages a combination of machine learning libraries, GPU-accelerated frameworks, and web technologies.

---

## 🖥️ Platform

* **Operating System**: Ubuntu 20.04.6 LTS
* **Python Version**: 3.10

---

## 🔍 Machine Learning

| Tool               | Purpose                                  |
| ------------------ | ---------------------------------------- |
| `scikit-learn`     | Isolation Forest, preprocessing          |
| `imbalanced-learn` | SMOTE for class balancing                |
| `RAPIDS cuML`      | GPU-accelerated Random Forest Classifier |
| `RAPIDS cuDF`      | cuML-compatible dataframes               |

---

## 🧠 ML Workflow

* **Preprocessing**: `StandardScaler`
* **Training**: cuML Random Forest + SMOTE
* **Anomaly Detection**: Isolation Forest

---

## 🌐 Web Application

| Tool       | Role                         |
| ---------- | ---------------------------- |
| Flask      | Backend REST API             |
| HTML/CSS   | Form and page structure      |
| JavaScript | Prediction logic & API calls |
| Chart.js   | Live chart visualization     |

---

## 📦 Dependency Management

* Virtual environment created using `venv`
* Dependencies managed via `requirements.txt`

---

## 🔒 Logging & Validation

* Python `logging` module for server logs
* Input validation and error handling for safe inference

This stack enables a fully offline, high-performance ML web app that runs on GPU-supported machines.
