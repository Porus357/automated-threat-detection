# 🚨 Network Anomaly Detection with GPU-Accelerated ML

**Completion Date:** June 2025  
**Platform:** Ubuntu 20.04 LTS (via WSL2)  
**Type:** Real-Time Network Traffic Anomaly Detection Web App

---

## 🌟 Overview

This project detects anomalous and malicious network traffic using **machine learning** with a hybrid approach. It leverages **GPU-accelerated Random Forest** (via RAPIDS cuML) for supervised classification and **Isolation Forest** (via scikit-learn) for unsupervised anomaly detection. A **Flask API** serves predictions, and a **responsive web dashboard** visualizes results in real time.

🎯 **Key Features**:
- 🛡️ Classifies network traffic as benign/malicious (Isolation Forest) and normal/anomalous (Random Forest).
- ⚡ Uses NVIDIA GPU acceleration for high-speed Random Forest training/prediction.
- ⚖️ Balances data with SMOTE for robust model performance.
- 🌐 Provides a user-friendly web interface for input and results.
- 🐳 Dockerized for easy deployment on WSL2 or Linux.

---

## 🧠 Model Details

### 🌲 Random Forest (GPU - RAPIDS cuML)
```python
RandomForestClassifier(
    n_estimators=2000,
    max_depth=150,
    min_samples_split=5,
    random_state=42,
    n_streams=1
)
```
- **Role**: Supervised classification of traffic as normal or anomalous.
- **Threshold**: Anomaly if confidence > 0.06.
- **Training**: Uses SMOTE-balanced CICIDS2017 dataset on GPU for speed (10-50x faster than CPU).

### 🧪 Isolation Forest (CPU - scikit-learn)
```python
IsolationForest(
    contamination=0.05,
    random_state=42,
    n_jobs=-1
)
```
- **Role**: Unsupervised detection of outliers (benign vs. malicious).
- **Training**: Fits on scaled features to identify anomalies.

### 📊 Input Features
The models use 13 key features from the CICIDS2017 dataset:
- Flow Duration
- Total Fwd Packets
- Total Backward Packets
- Fwd Packet Length Mean
- Bwd Packet Length Mean
- Flow Bytes/s
- Flow Packets/s
- Fwd PSH Flags
- Bwd PSH Flags
- SYN Flag Count
- ACK Flag Count
- URG Flag Count
- Packet Length Mean

---

## 🛠️ Tech Stack
| **Layer**         | **Tools/Frameworks**                     |
|--------------------|------------------------------------------|
| **Language**      | Python 3.9                               |
| **ML Models**     | scikit-learn, RAPIDS cuML, cuDF          |
| **Data Balancing**| imbalanced-learn (SMOTE)                 |
| **Backend**       | Flask                                    |
| **Frontend**      | HTML, CSS, JavaScript                    |
| **Deployment**    | Docker, WSL2, NVIDIA Container Toolkit   |
| **Logging**       | Python logging (outputs to `atd.log`)   |

---

## 📂 Project Structure
```
network-anomaly-detection/
├── atd.py                # Main Flask app and ML logic
├── DATADIR/              # CICIDS2017 CSV files
├── iso_forest.pkl        # Saved Isolation Forest model
├── rf_model.pkl          # Saved Random Forest model
├── scaler.pkl            # Saved feature scaler
├── atd.log               # Prediction and error logs
├── Dockerfile            # Docker configuration
├── README.md             # This file
```

---

## 🚀 Getting Started

### Prerequisites
- **Hardware**: NVIDIA GPU (e.g., RTX 3060) with CUDA support.
- **OS**: Windows 10/11 with WSL2 or Linux (Ubuntu 20.04 LTS).
- **Software**:
  - Docker Desktop (Windows) or Docker (Linux).
  - NVIDIA drivers (465.89+).
- **Dataset**: CICIDS2017 CSV files (place in `DATADIR/`).

### Installation
1. **Set Up WSL2 (Windows Only)**:
   ```powershell
   wsl --install
   ```
   Install Ubuntu 20.04 LTS from the Microsoft Store, then:
   ```powershell
   wsl --set-default Ubuntu-20.04
   ```

2. **Install NVIDIA Drivers**:
   - Download from [nvidia.com](https://www.nvidia.com/Download/index.aspx).
   - Verify: `nvidia-smi` in PowerShell.

3. **Install Docker Desktop**:
   - Download from [docker.com](https://www.docker.com/products/docker-desktop/).
   - Enable WSL2: Settings > General > "Use the WSL 2 based engine".
   - Enable WSL integration: Settings > Resources > WSL Integration > Select Ubuntu-20.04.

4. **Install NVIDIA Container Toolkit**:
   In Ubuntu 20.04 terminal (`wsl -d Ubuntu-20.04`):
   ```bash
   sudo apt update && sudo apt install -y curl
   distribution=ubuntu20.04
   curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo tee /etc/apt/trusted.gpg.d/nvidia-docker.gpg > /dev/null
   curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
   sudo apt-get update
   sudo apt-get install -y nvidia-docker2
   ```
   Test GPU:
   ```bash
   docker run --gpus all nvidia/cuda:11.8.0-base-ubuntu20.04 nvidia-smi
   ```

5. **Clone Repository**:
   ```bash
   git clone https://github.com/your-username/your-repo.git
   cd your-repo
   ```

6. **Prepare Dataset**:
   - Place CICIDS2017 CSVs (e.g., `Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv`) in `/mnt/c/atd/DATADIR`.

7. **Build and Run Docker**:
   Create `Dockerfile`:
   ```dockerfile
   FROM rapidsai/rapidsai-core:23.10-cuda11.8-runtime-ubuntu20.04-py3.9
   WORKDIR /app
   RUN pip install --upgrade pip && \
       pip install flask imblearn joblib
   COPY . /app
   EXPOSE 5000
   CMD ["python", "atd.py"]
   ```
   Build:
   ```bash
   docker build -t anomaly-detection:latest .
   ```
   Run:
   ```bash
   docker run --gpus all -p 5000:5000 -v /mnt/c/atd/DATADIR:/mnt/c/atd/DATADIR anomaly-detection:latest
   ```

---

## 🎮 Usage

### Web Dashboard
1. Open `http://localhost:5000` in a browser.
2. Enter network traffic features (e.g., Flow Duration, Total Fwd Packets).
3. Click "Predict" to see:
   - Isolation Forest: benign or malicious
   - Random Forest: normal or anomaly
   - Confidence score (Random Forest probability)

### API Endpoint
- **POST** to `http://localhost:5000/predict`:
  ```json
  {
    "Flow Duration": 100.0,
    "Total Fwd Packets": 10.0,
    "Total Backward Packets": 5.0,
    "Fwd Packet Length Mean": 50.0,
    "Bwd Packet Length Mean": 60.0,
    "Flow Bytes/s": 1000.0,
    "Flow Packets/s": 20.0,
    "Fwd PSH Flags": 0.0,
    "Bwd PSH Flags": 0.0,
    "SYN Flag Count": 1.0,
    "ACK Flag Count": 1.0,
    "URG Flag Count": 0.0,
    "Packet Length Mean": 55.0
  }
  ```
- **Response**:
  ```json
  {
    "isolation_forest_result": "benign",
    "random_forest_result": "normal",
    "confidence": 0.03
  }
  ```

---

## 🛠️ Troubleshooting
- **DNS Errors**: If `sudo apt-get update` fails:
  ```bash
  sudo nano /etc/resolv.conf
  ```
  Add:
  ```
  nameserver 8.8.8.8
  nameserver 8.8.4.4
  ```
  Lock: `sudo chmod -w /etc/resolv.conf`.
- **GPU Not Detected**: Ensure NVIDIA drivers are installed and `nvidia-smi` works.
- **Dataset Issues**: Verify CICIDS2017 CSVs are in `DATADIR/`.
- **Logs**: Check `atd.log` for errors.

---

## 📚 Skills Demonstrated
- 🧠 Machine learning (supervised and unsupervised)
- ⚖️ Handling imbalanced datasets (SMOTE)
- ⚡ GPU-accelerated ML with RAPIDS
- 🌐 REST API development with Flask
- 🎨 Responsive web dashboard design
- 🐳 Docker deployment with WSL2

---

## 🤝 Contributing
Open issues or PRs on GitHub to improve the project!

---

## 📜 License
MIT License. See [LICENSE](LICENSE) for details.
