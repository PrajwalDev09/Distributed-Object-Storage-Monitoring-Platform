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

```mermaid
graph LR
    subgraph Client ["1. Client Layer"]
        UI["💻 StoragePulse Dashboard<br/><i>(Tailwind CSS + Chart.js)</i>"]
    end

    subgraph Server ["2. Application Layer"]
        API["⚡ FastAPI Async Engine<br/><i>(Data Normalization & Probing)</i>"]
        Alerts["🚨 Alert Engine<br/><i>(Webhook / Terminal)</i>"]
    end

    subgraph Storage ["3. Storage Layer"]
        Ceph["📦 Ceph RADOS Gateway<br/><i>(On-Premises S3)</i>"]
        AWS["☁️ AWS S3 Cloud<br/><i>(Public Cloud)</i>"]
    end

    UI -->|HTTP GET /api/metrics| API
    API -->|Boto3 S3 Protocol| Ceph
    API -->|Boto3 S3 Protocol| AWS
    API -.->|Threshold Breach| Alerts

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

## 🚀 Quick Start Guide

### Prerequisites
* Python 3.10 or higher
* Docker Desktop installed and running
* An active AWS Account (or enable `MOCK_MODE=true` for offline testing)

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
Step 2: Environment Configuration
Copy .env.example to .env:
code
Bash
cp .env.example .env
Configure your .env credentials file:
code
Env
# Web Server Configuration
PORT=8000
MOCK_MODE=false

# AWS S3 Configuration
AWS_ACCESS_KEY_ID=YOUR_REAL_AWS_ACCESS_KEY
AWS_SECRET_ACCESS_KEY=YOUR_REAL_AWS_SECRET_KEY
AWS_REGION=us-east-1

# Local Ceph / S3 Object Gateway Configuration (Port 8088)
CEPH_RGW_ENDPOINT=http://127.0.0.1:8088
CEPH_ACCESS_KEY=ceph_demo_access_key
CEPH_SECRET_KEY=ceph_demo_secret_key
Step 3: Run Local Object Store & Web Server
code
Bash
# 1. Start Local S3/Ceph Container on Port 8088
docker run -d --name ceph-rgw -p 8088:9000 \
  -e "MINIO_ROOT_USER=ceph_demo_access_key" \
  -e "MINIO_ROOT_PASSWORD=ceph_demo_secret_key" \
  minio/minio server /data

# 2. Launch FastAPI Web Application
uvicorn app.main:app --reload --port 8000
Open your browser and navigate to:
👉 http://127.0.0.1:8000
Step 4: Run Real-Time Traffic Generator (Optional)
Open a second terminal window in the project folder and start the synthetic traffic generator:
code
Bash
# Activate Virtual Environment
.\venv\Scripts\Activate.ps1

# Run Traffic Simulator
python simulate_traffic.py
Watch the dashboard charts and bucket explorer table update live as files are uploaded!

📂 Repository Directory Layout
code
Text
storage-monitor/
├── .env.example             # Environment configuration template
├── .gitignore                # Git file exclusion rules
├── requirements.txt          # Python library dependencies
├── simulate_traffic.py       # Asynchronous load & traffic simulator
├── app/
│   ├── config.py             # Configuration loader
│   ├── main.py               # FastAPI server & API route handlers
│   ├── notifier.py           # Webhook & terminal alert manager
│   ├── collectors/
│   │   ├── aws_collector.py  # AWS S3 Boto3 metrics fetcher
│   │   └── ceph_collector.py # Ceph RGW Boto3 metrics fetcher
│   └── static/
│       └── index.html        # Interactive Single-Page Application UI
🔌 API Endpoint Documentation
Endpoint	Method	Description

/	GET	Serves the main single-page monitoring dashboard UI

/api/metrics	GET	Fetches aggregated storage stats, object counts, latency probes, and bucket lists

/api/health	GET	System health check and liveness probe

💡 System Design Talking Points (For Interviews)

Protocol Normalization: Abstracted heterogeneous response metadata from AWS S3 REST API and Ceph RADOS Gateway into a unified JSON telemetry schema.
Non-Blocking Architecture: Applied asynchronous endpoints in FastAPI to keep user dashboard responsiveness sub-10ms regardless of external API latencies.
Observability & Fault Isolation: Built threshold-based alerting to isolate local network bottlenecks from public cloud service degradation.

🤝 Contributing & License

Contributions, issues, and feature requests are welcome! Feel free to check the issues page.
This project is licensed under the MIT License.
