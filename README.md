Network Anomaly Detection with GPU-Accelerated Machine Learning
This project implements a network anomaly detection system using GPU-accelerated Random Forest (via RAPIDS cuML) and CPU-based Isolation Forest (via scikit-learn). It processes the CICIDS2017 dataset to classify network traffic as benign/malicious and normal/anomalous, served through a Flask API with a web dashboard for real-time predictions.
Features

Hybrid ML Approach: Combines supervised (Random Forest) and unsupervised (Isolation Forest) learning for robust anomaly detection.
GPU Acceleration: Leverages NVIDIA GPUs with cuML for high-performance Random Forest training and prediction.
Web Interface: Flask-based API and dashboard for submitting network traffic data and viewing predictions.
Scalable Deployment: Dockerized setup for easy deployment in WSL2 or Linux environments.
Data Processing: Handles the CICIDS2017 dataset with preprocessing, SMOTE for class balancing, and feature scaling.

Prerequisites

Hardware: NVIDIA GPU with CUDA support (e.g., RTX 3060).
OS: Windows 10/11 with WSL2 or Linux (Ubuntu 20.04 recommended).
Software:
Docker Desktop (Windows) or Docker (Linux).
NVIDIA drivers (465.89 or later).
Python 3.9 (included in Docker image).


Dataset: CICIDS2017 CSV files (place in DATADIR folder).

Installation
1. Set Up WSL2 (Windows Only)

Install WSL2:wsl --install


Install Ubuntu 20.04 from the Microsoft Store.
Set Ubuntu 20.04 as default:wsl --set-default Ubuntu-20.04



2. Install NVIDIA Drivers

Download and install NVIDIA drivers from nvidia.com.
Verify GPU:nvidia-smi



3. Install Docker Desktop

Download Docker Desktop.
Enable WSL2 backend: Settings > General > "Use the WSL 2 based engine".
Enable WSL integration: Settings > Resources > WSL Integration > Select Ubuntu-20.04.

4. Install NVIDIA Container Toolkit
In Ubuntu 20.04 terminal (wsl -d Ubuntu-20.04):
sudo apt update && sudo apt install -y curl
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y nvidia-docker2

Test GPU:
docker run --gpus all nvidia/cuda:11.8.0-base-ubuntu20.04 nvidia-smi

5. Clone the Repository
git clone https://github.com/your-username/your-repo.git
cd your-repo

6. Prepare Dataset

Download CICIDS2017 CSV files (listed in atd.py).
Place them in a DATADIR folder (e.g., /mnt/c/atd/DATADIR on WSL2).

7. Build and Run Docker Container
Create a Dockerfile:
FROM rapidsai/rapidsai-core:23.10-cuda11.8-runtime-ubuntu20.04-py3.9
WORKDIR /app
RUN pip install --upgrade pip && \
    pip install flask imblearn joblib
COPY . /app
EXPOSE 5000
CMD ["python", "atd.py"]

Build:
docker build -t anomaly-detection:latest .

Run:
docker run --gpus all -p 5000:5000 -v /path/to/your/DATADIR:/mnt/c/atd/DATADIR anomaly-detection:latest

Usage

Access Dashboard: Open http://localhost:5000 in a browser.
Input Features: Enter network traffic data (e.g., Flow Duration, Total Fwd Packets).
Get Predictions: Submit to see Isolation Forest (benign/malicious) and Random Forest (normal/anomaly) results with confidence scores.
API Endpoint:
POST to http://localhost:5000/predict with JSON:{
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


Response:{
  "isolation_forest_result": "benign",
  "random_forest_result": "normal",
  "confidence": 0.03
}





Project Structure

atd.py: Main script with data preprocessing, model training, and Flask API.
DATADIR/: Directory containing CICIDS2017 CSV files.
iso_forest.pkl, rf_model.pkl, scaler.pkl: Saved models and scaler.
atd.log: Log file for predictions and errors.

Notes

GPU Performance: Random Forest uses cuML for 10-50x faster training on large datasets.
Model Tuning: Adjust n_estimators, max_depth, or contamination in atd.py for better accuracy.
Troubleshooting: Check atd.log for errors. Ensure DATADIR path is correct.

Contributing
Feel free to open issues or PRs on GitHub to enhance the project!
License
MIT License
