# Assignment-1-cloud-computing
# CS6847 Assignment 1 – Docker Swarm & Kubernetes Deployment with Client Testing

## 📌 Project Overview
This project shows how to deploy a simple **Flask web service** using **Docker Swarm** and **Kubernetes (via Minikube)**.  
It also includes a **Python client** to perform load testing and collect results, making it easy to run reproducible experiments.

---

## ⚙️ Requirements
- Docker & Docker Swarm
- Minikube (for Kubernetes)
- Python 3.11+
- Virtual environment (recommended)

---

## 📂 Folder Structure

---

## 🏗️ Architecture & Components
- **`app/`** → Flask app containerized using Docker.  
- **`client/`** → Load testing scripts.  
- **`kubernetes/`** → YAML configs for deployment, service & autoscaling.  
- **`results/`** → Benchmark outputs for Swarm & Kubernetes.  

---

## 🔑 Setup

### 1️⃣ Clone repository
```bash
git clone https://github.com/amar-at-iitm/cs6847_assignment1
cd cs6847_assignment1
