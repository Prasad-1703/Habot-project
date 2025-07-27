# 🧠 Simple Document Extraction App — Kubernetes Deployment Guide

This guide explains how to deploy the **Simple Extraction Django App** (developed by [varal-uae](https://github.com/varal-uae/Simple-Extraction-Demo-Track)) on **Kubernetes using Minikube** with PostgreSQL as the database, environment variables via ConfigMaps & Secrets, and static file handling.

---

## 📁 Repositories Used

* 🔹 **Source Code & Dockerfile**: [Simple-Extraction-Demo-Track](https://github.com/varal-uae/Simple-Extraction-Demo-Track.git)
* 🔸 **Kubernetes YAML Files**: [Habot-project](https://github.com/Prasad-1703/Habot-project.git)

---

## ⚙️ Prerequisites

Ensure you have the following tools installed on your local machine:

* [Docker Desktop](https://www.docker.com/products/docker-desktop) (For Windows)
* [Minikube](https://minikube.sigs.k8s.io/docs/start/)
* [kubectl](https://kubernetes.io/docs/tasks/tools/)
* Git

---

## 🚀 Setup Steps

### 1. 📂 Clone Repositories

```bash
# Source code for Docker image
git clone https://github.com/varal-uae/Simple-Extraction-Demo-Track.git

# Kubernetes YAML setup
git clone https://github.com/Prasad-1703/Habot-project.git
cd Habot-project
```

---

### 2. 🚢 Start Minikube

```bash
minikube start --driver=docker
minikube addons enable ingress
```

---

### 3. 🐳 Build & Push Docker Image

#### 🔹 Option 1: Use Prebuilt Image (Quick Start)

Already used in the deployment YAML:

```yaml
image: prasad1703/simpleextraction:latest
```

#### 🔸 Option 2: Build Your Own Image

```bash
cd Simple-Extraction-Demo-Track
docker build -t yourdockerhubusername/simpleextraction:v1 .
docker push yourdockerhubusername/simpleextraction:v1
```

Update your `deployment.yaml` accordingly:

```yaml
image: yourdockerhubusername/simpleextraction:v1
```

---

### 4. 🔐 Create ConfigMap & Secrets

```bash
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
```

---

### 5. 📄 Deploy PostgreSQL

```bash
kubectl apply -f postgres-deployment.yaml
kubectl apply -f postgres-service.yaml
```

---

### 6. 🛠 Deploy Django App

```bash
kubectl apply -f django-deployment.yaml
kubectl apply -f django-service.yaml
```

---

### 7. 🏁 Run Migrations Automatically (Init Container)

Ensure `initContainers` are included in the deployment like this:

```yaml
initContainers:
  - name: migrate
    image: prasad1703/simpleextraction:latest
    command: ["python", "manage.py", "migrate"]
    envFrom:
      - configMapRef:
          name: django-config
      - secretRef:
          name: django-secret
```

---

### 8. 🌐 Set Up Ingress

```bash
kubectl apply -f ingress.yaml
```

Update Windows hosts file (`C:\Windows\System32\drivers\etc\hosts`):

```text
192.168.49.2 demo.local
```

Replace with your Minikube IP:

```bash
minikube ip
```

---

### 9. 🌍 Access the App

Start the tunnel:

```bash
minikube tunnel
```

Then go to:

```text
http://demo.local
```

Or:

```bash
minikube service django
```

---

## ✅ Final Notes

* Make sure PostgreSQL and Django pods are in **Running** state.
* If using a new PostgreSQL version and hitting version mismatch errors, delete Minikube volumes:

```bash
minikube delete --all
```

* Logs can be viewed with:

```bash
kubectl logs <pod-name>
```

---

## 🙌 Credits

* 💻 App Source: [varal-uae/Simple-Extraction-Demo-Track](https://github.com/varal-uae/Simple-Extraction-Demo-Track)
* 🔧 Kubernetes Configs: [Prasad-1703/Habot-project](https://github.com/Prasad-1703/Habot-project)
