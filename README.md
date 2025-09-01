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
