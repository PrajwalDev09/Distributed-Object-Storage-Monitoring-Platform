# ⚡ StoragePulse: Distributed Object Storage Monitoring Platform

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.109.0-009688.svg)](https://fastapi.tiangolo.com/)
[![Docker](https://img.shields.io/badge/Docker-Supported-2496ED.svg)](https://www.docker.com/)
[![AWS S3](https://img.shields.io/badge/AWS_S3-Supported-FF9900.svg)](https://aws.amazon.com/s3/)
[![Ceph](https://img.shields.io/badge/Ceph_RGW-Supported-E05243.svg)](https://ceph.io/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

An enterprise-grade hybrid storage observability platform designed to aggregate telemetry, monitor API latency, track bucket allocations, and detect failures across on-premises **Ceph RADOS Gateway (RGW)** clusters and public **AWS S3** cloud infrastructure.

---

## 🌟 Executive Summary

Modern enterprises frequently adopt a **hybrid cloud object storage model**:
* **On-Premises Ceph Clusters**: For high-volume data, compliance, and low cost per terabyte.
* **AWS S3**: For global distribution, cloud bursting, and managed durability.

However, operating disjointed storage systems creates operational visibility gaps. **StoragePulse** unifies storage telemetry into a single, real-time dashboard—normalizing S3 protocol metrics, probing round-trip latency, and triggering automated failure alerts.

---

## 📐 System Architecture

<img width="422" height="351" alt="System Architecture Diagram" src="https://github.com/user-attachments/assets/bf39f583-985c-434e-96cd-fe5d97dadf05" />

---

## ✨ Key Features

* 📊 **Unified Storage Capacity Tracking**: Aggregates total used bytes and total object count across all clouds in real time.
* ⚡ **Real-Time API Latency Probing**: Measures active HTTP/S3 round-trip times (RTT) in milliseconds for both local Ceph hardware and remote AWS regions.
* 🗂️ **Multi-Tenant Bucket Explorer**: Single-pane-of-glass table displaying object counts, bucket sizes, regions, and creation dates with client-side filtering.
* 🚨 **Automated Anomaly Alerting**: Fires automated alerts when storage endpoints become unreachable or latency breaches set thresholds (>200ms Ceph, >500ms AWS).
* 🔄 **Synthetic Traffic Generator**: Includes an asynchronous load generator (`simulate_traffic.py`) to simulate active file uploads and live metric updates.
* 🧪 **Dual Engine Mode**: Supports **Live Hardware/Cloud Mode** and zero-dependency **Simulation Mode** (`MOCK_MODE=true`).

---

## 🛠️ Tech Stack

* **Backend Framework**: Python 3.10+, FastAPI, Boto3 SDK, Uvicorn, Pydantic, Dotenv
* **Frontend UI**: HTML5, Tailwind CSS, Chart.js, FontAwesome
* **DevOps & Containers**: Docker, MinIO / Ceph RADOS Gateway
* **Testing & Traffic**: Asynchronous Boto3 load simulator script

---

### Step 1: Clone Repository & Setup Virtual Environment

```bash
# Clone the repository
git clone https://github.com/YOUR_GITHUB_USERNAME/storage-monitor.git
cd storage-monitor

# Create virtual environment
python -m venv venv

# Activate Virtual Environment
# On Windows PowerShell:
.\venv\Scripts\Activate.ps1
# On Linux / macOS:
source venv/bin/activate

# Install Dependencies
pip install -r requirements.txt




Open a second terminal window in the project folder and start the synthetic
traffic generator:

# Activate Virtual Environment
.\venv\Scripts\Activate.ps1

# Run Traffic Simulator
python simulate_traffic.py


🤝 Contributing & License

Contributions, issues, and feature requests are welcome! Feel free to check the
issues page.

This project is licensed under the MIT License.

