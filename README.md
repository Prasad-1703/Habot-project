
# 🧠 Simple Document Extraction App — Kubernetes Deployment Guide

This guide explains how to deploy the **Simple Extraction Django App** (developed by [varal-uae](https://github.com/varal-uae/Simple-Extraction-Demo-Track)) on **Kubernetes using Minikube** with PostgreSQL as the database, environment variables via ConfigMaps & Secrets, and static file handling.

---

## 📁 Repositories Used

- 🔹 **Source Code & Dockerfile**: [Simple-Extraction-Demo-Track](https://github.com/varal-uae/Simple-Extraction-Demo-Track.git)
- 🔸 **Kubernetes YAML Files**: [Habot-project](https://github.com/Prasad-1703/Habot-project.git)

---

## ⚙️ Prerequisites

Ensure you have the following tools installed on your local machine:

- [Docker Desktop](https://www.docker.com/products/docker-desktop) (for Windows)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- Git

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

Already referenced in the deployment file:

```yaml
image: prasad1703/simpleextraction:latest
```

#### 🔸 Option 2: Build Your Own Image

```bash
cd Simple-Extraction-Demo-Track
docker build -t yourdockerhubusername/simpleextraction:v1 .
docker push yourdockerhubusername/simpleextraction:v1
```

Then update `deployment.yaml`:

```yaml
image: yourdockerhubusername/simpleextraction:v1
```

---

### 4. ⚙️ Create ConfigMap & Secrets

```bash
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
```

---

### 5. 🗄 Deploy PostgreSQL

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

### 7. 🏁 Migrate the Database

#### ✅ Option A: Automatic Migrations Using Init Container

`initContainers` section in your deployment will handle it automatically on pod creation:

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

#### 🛠 Option B: Manual Migrations Using `kubectl exec`

You can also manually run migrations using this command:

```bash
kubectl exec -it <django-pod-name> -- python manage.py migrate
```

Find pod name with:

```bash
kubectl get pods
```

---

### 8. 🌐 Set Up Ingress

```bash
kubectl apply -f ingress.yaml
```

Then add the IP to your `hosts` file:

```plaintext
C:\Windows\System32\drivers\etc\hosts

192.168.49.2 demo.local
```

Replace `192.168.49.2` with the output of:

```bash
minikube ip
```

---

### 9. 🌍 Access the App

Start the tunnel:

```bash
minikube tunnel
```

Then open in your browser:

```plaintext
http://demo.local
```

If you're using NodePort instead:

```bash
minikube service django
```

---

### 📦 ✅ Run the Full Setup with One Command (If YAMLs are in One Folder)

```bash
kubectl apply -f .
```

Make sure all `.yaml` files (ConfigMap, Secret, Postgres, Django, Ingress) are in the current directory.

---

## ✅ Final Checks

- Pods should be in **Running** state: `kubectl get pods`
- Logs (if needed): `kubectl logs <pod-name>`
- Delete minikube volumes if PostgreSQL version mismatch occurs:

```bash
minikube delete --all
```

---

## 🙌 Credits

- 💻 App Source: [varal-uae/Simple-Extraction-Demo-Track](https://github.com/varal-uae/Simple-Extraction-Demo-Track)
- 🔧 Kubernetes Configs: [Prasad-1703/Habot-project](https://github.com/Prasad-1703/Habot-project)
