# cs6847 Assignment 1 – Docker Swarm & Kubernetes Deployment with Client Testing

## Project Overview
This project demonstrates how to containerize and deploy a small **Flask-based API service** using both **Docker Swarm** and **Kubernetes (Minikube)**.  
It also includes a simple **Python client** that can generate traffic for benchmarking.  
The goal is to provide a reproducible workflow for experimenting with scaling and performance testing.  

---

## Requirements
- Docker installed (with Swarm mode enabled)  
- Minikube (for running Kubernetes locally)  
- Python 3.11+  
- A virtual environment for Python dependencies (recommended)  

---

## Folder Structure
assignment1/  

│── .dockerignore              # Excludes unnecessary files from Docker builds  
│── .gitignore                 # Git ignored files  
│── run_swarm_test.sh          # Helper script for Docker Swarm test  
│── run_k8s_test.sh            # Helper script for Kubernetes test  
│── run_all_tests.sh           # Runs all tests together  
│── README.md                  # Documentation  
│  
├── app/                       # Flask web service code  
│   ├── requirements.txt        # Python package list  
│   ├── app.py                  # Main entrypoint  
│   ├── Dockerfile              # Image build instructions  
│   └── __init__.py             # Marks package directory  
│  
├── kubernetes/                # YAML configs for Kubernetes  
│   ├── deployment.yaml         # Deployment (3 replicas by default)  
│   ├── hpa.yaml                # Horizontal Pod Autoscaler (scales up to 10 pods)  
│   └── service.yaml            # Exposes the Flask service  
│  
├── client/                    # Python client for load testing  
│   ├── client.py               # Request generator  
│   └── utils.py                # Utility functions (logging, metrics)  
│  
└── results/                   # Stores output of tests  
    ├── docker_response_10  
    ├── docker_response_10000  
    ├── kubernetes_response_10  
    └── kubernetes_response_10000  

---

## Architecture & Components
- **app/** → Flask microservice packaged in Docker  
- **client/** → Python client for sending load requests  
- **kubernetes/** → Configs for Deployment, Service, and HPA  
- **results/** → Output and logs from test executions  

---
## Key Workflows
- **Build & Test Locally**:
  - Install Python dependencies: 
  ```bash
  pip install -r app/requirements.txt
  ```
  - Run Flask app locally: 
  ```bash
  python app/app.py
  ```

- **Docker Swarm**:
  - Build image: 
  ```bash
  docker build -t flask-app:latest app/
  ```
  - Init Swarm: 
  ```bash
  docker swarm init
  ```
  - Deploy: 
  ```bash
  docker service create --name flask-swarm --replicas 3 -p 5000:5000 flask-app:latest
  ```


- **Kubernetes (Minikube)**:
  - Start: 
  ```bash
  minikube start
  ```
  - Use Minikube Docker: 
  ```bash
  eval $(minikube docker-env)
  ```
  - Build image: 
  ```bash
  docker build -t flask-app:latest app/
  ```
  - Deploy: 
  ```bash
  kubectl apply -f kubernetes/
  ```
  - Get service URL: 
  ```bash
  minikube service flask-service --url
  ```
- **Client Testing**:
  - Example: 
    ```bash
    cd client
    ```

    ```bash
    python client.py --target http://<IP>:<PORT> --rate 10 --output ../results/docker_response_10
    python client.py --target http://<IP>:<PORT> --rate 10000 --output ../results/docker_response_10000
    ```

    ```bash
    python client.py --target http://<IP>:<PORT> --rate 10 --output ../results/kubernetes_response_10
    python client.py --target http://<IP>:<PORT> --rate 10000 --output ../results/kubernetes_response_10000
    ```


  - Adjust `--rate` and `--output` as needed for experiments.

## Conventions & Patterns
- All service endpoints are exposed on `/` (root) and listen on port 5000 (Docker) or as mapped by Kubernetes.
- Results are always written to the `results/` directory with descriptive filenames.
- Kubernetes uses autoscaling (see `hpa.yaml`), Swarm uses fixed replicas.
- No authentication or persistent storage is used; the service is stateless.

## Integration Points
- The client is decoupled and can target any HTTP endpoint.
- Docker and Kubernetes deployments are independent; do not run both simultaneously on the same port.
- All configuration is via YAML (Kubernetes) or CLI (Swarm).
