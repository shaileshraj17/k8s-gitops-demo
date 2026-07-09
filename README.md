# Kubernetes GitOps Deployment with Helm & ArgoCD

A production-style GitOps pipeline that containerizes a Python web application and deploys it to a Kubernetes cluster using Helm for templated manifest management and ArgoCD for automated continuous delivery.

---

## 🚀 Live Demo

> `Hello from Kubernetes GitOps Demo - Version: v1`

Application deployed and synced via ArgoCD — any commit to the `main` branch automatically triggers a live deployment update without manual `kubectl` intervention.

---

## 🏗️ Architecture

```
GitHub Repository (Source of Truth)
          │
          ▼
    ArgoCD (Auto Sync)
          │
          ▼
   Helm Chart Deployment
          │
          ▼
Kubernetes Cluster (Minikube)
          │
          ▼
  Python App (2 Replicas)
          │
          ▼
  Service → localhost:5000
```

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **Docker** | Containerize the Python Flask application |
| **Kubernetes** | Container orchestration and workload management |
| **Helm** | Templated, reusable Kubernetes manifest management |
| **ArgoCD** | GitOps-based continuous delivery and auto sync |
| **GitHub** | Single source of truth for cluster state |
| **Python (Flask)** | Sample web application |

---

## 📁 Project Structure

```
k8s-gitops-demo/
├── app/
│   ├── app.py                  # Python Flask application
│   ├── Dockerfile              # Container image definition
│   └── requirements.txt        # Python dependencies
├── helm-chart/
│   ├── Chart.yaml              # Helm chart metadata
│   ├── values.yaml             # Configurable deployment values
│   └── templates/
│       ├── deployment.yaml     # Kubernetes Deployment manifest
│       └── service.yaml        # Kubernetes Service manifest
└── argocd/
    └── application.yaml        # ArgoCD Application manifest
```

---

## ⚙️ How It Works

### 1. Containerization
The Python Flask app is containerized using Docker and the image is pushed to Docker Hub (`shailesh1717/k8s-gitops-demo:v1`).

### 2. Helm Chart
Helm manages the Kubernetes manifests with a templated, DRY approach. The `values.yaml` file controls all configurable parameters — image tag, replica count, service port — making environment-specific deployments clean and consistent.

### 3. GitOps with ArgoCD
ArgoCD is installed on the Kubernetes cluster and configured to watch this GitHub repository. When a change is pushed to the `main` branch, ArgoCD automatically detects the drift between the desired state (Git) and live state (cluster) and syncs them — no manual `kubectl apply` needed.

---

## 🚦 Setup & Deployment Guide

### Prerequisites
- Docker Desktop
- Minikube
- kubectl
- Helm
- WSL2 (if on Windows)

### Step 1 — Start Minikube
```bash
minikube start --driver=docker
```

### Step 2 — Install ArgoCD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Step 3 — Wait for ArgoCD Pods
```bash
kubectl get pods -n argocd
```
Wait until all pods show `1/1 Running`.

### Step 4 — Access ArgoCD UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Open `https://localhost:8080` in your browser.

Get the admin password:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Step 5 — Deploy Application
```bash
kubectl apply -f argocd/application.yaml -n argocd
```

### Step 6 — Access the App
```bash
kubectl port-forward svc/k8s-gitops-demo -n default 5000:80
```
Open `http://localhost:5000` — you should see:
> `Hello from Kubernetes GitOps Demo - Version: v1`

---

## 🔄 GitOps in Action — Test It Yourself

To see GitOps auto-sync working live:

1. Update the `tag` in `helm-chart/values.yaml` from `v1` to `v2`
2. Build and push a new Docker image tagged `v2`
3. Commit and push to `main`
4. Watch ArgoCD automatically detect the change and redeploy — no manual steps needed

---

## 📊 Deployment Details

| Parameter | Value |
|---|---|
| Replicas | 2 |
| Image | `shailesh1717/k8s-gitops-demo:v1` |
| Container Port | 5000 |
| Service Port | 80 |
| Service Type | ClusterIP |
| Sync Policy | Automatic |
| Namespace | default |

---

## 🎯 Key Concepts Demonstrated

- **Declarative Infrastructure** — cluster state defined entirely in Git
- **GitOps Workflow** — Git as single source of truth for deployments
- **Helm Templating** — reusable, configurable Kubernetes manifests
- **Automated Sync** — ArgoCD eliminates manual deployment steps
- **Container Orchestration** — multi-replica deployment with Kubernetes

---

## 👤 Author

**Shailesh Raj**
- GitHub: [shaileshraj17](https://github.com/shaileshraj17)
- LinkedIn: [Shailesh LinkedIn](https://www.linkedin.com/in/shailesh)
- Docker Hub: [shailesh1717](https://hub.docker.com/u/shailesh1717)
