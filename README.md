# cs6847 Assignment 1 – Docker Swarm & Kubernetes Deployment with Client Testing

## Project Overview
This assignment showcases how to deploy a small **Flask-based web service** using both **Docker Swarm** and **Kubernetes (Minikube)**.  
It also includes a lightweight **Python client** for performing load testing. The project is designed so that experiments and benchmarks can be repeated easily.

---

## Requirements
- Docker & Docker Swarm
- Minikube (for Kubernetes testing)
- Python 3.11 or newer
- Virtual environment setup (recommended)

---

## Folder Structure
assignment1/

│── .dockerignore              # Skip unnecessary files in build  
│── .gitignore                 
│── run_swarm_test.sh  
│── run_k8s_test.sh  
│── run_all_tests.sh  
│── README.md                  
│  
├── app/                       # Core web service (Flask app)  
│   ├── requirements.txt                  
│   ├── app.py                 # Flask entrypoint  
│   ├── Dockerfile             # Image build instructions  
│   └── __init__.py            # Empty file, keeps directory as a package  
│  
├── kubernetes/                # Kubernetes deployment configs  
│   ├── deployment.yaml        # Deployment with at least 3 replicas  
│   ├── hpa.yaml               # Horizontal Pod Autoscaler (scales up to 10)  
│   └── service.yaml           # Service for exposing Flask app  
│  
├── client/                    # Client-side load testing code  
│   ├── client.py              # Request generator + response tracker  
│   └── utils.py               # Utility functions (averages, writing output)  
│  
└── results/                   # Folder for test run outputs  
    ├── docker_response_10  
    ├── docker_response_10000  
    ├── kubernetes_response_10  
    └── kubernetes_response_10000  

---

## Architecture & Components
- **app/** → Flask web service wrapped in Docker.  
- **client/** → Python-based test client.  
- **kubernetes/** → Deployment, Service, and Autoscaler configs (3–10 replicas).  
- **results/** → Stores benchmarking logs from client runs.  

---

## Clone Repository
```bash
git clone https://github.com/amar-at-iitm/cs6847_assignment1
cd cs6847_assignment1
# ==============================
# 1. Build & Test Locally
# ==============================

# Install dependencies
pip install -r app/requirements.txt

# Run Flask app locally
python app/app.py


# ==============================
# 2. Docker Swarm
# ==============================

# Build Docker image
docker build -t flask-app:latest app/

# Initialize Swarm
docker swarm init

# Deploy service with 3 replicas
docker service create --name flask-swarm --replicas 3 -p 5000:5000 flask-app:latest

# Check service status
docker service ls
docker ps


# ==============================
# 3. Kubernetes (Minikube)
# ==============================

# Start Minikube
minikube start

# Point Docker to Minikube
eval $(minikube docker-env)

# Build image inside Minikube
docker build -t flask-app:latest app/

# Deploy manifests (deployment, service, hpa)
kubectl apply -f kubernetes/

# Verify deployment
kubectl get pods
kubectl get svc

# Get service URL
minikube service flask-service --url


# ==============================
# 4. Client Testing
# ==============================

cd client

# Run tests against Docker service
python client.py --target http://<DOCKER_IP>:5000 --rate 10 --output ../results/docker_response_10
python client.py --target http://<DOCKER_IP>:5000 --rate 10000 --output ../results/docker_response_10000

# Run tests against Kubernetes service
python client.py --target http://<K8S_URL> --rate 10 --output ../results/kubernetes_response_10
python client.py --target http://<K8S_URL> --rate 10000 --output ../results/kubernetes_response_10000



