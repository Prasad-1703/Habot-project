# 🧠 Simple Document Extraction App — Kubernetes Deployment Guide

This guide explains how to deploy the **Simple Extraction Django App** (developed by [varal-uae](https://github.com/varal-uae/Simple-Extraction-Demo-Track)) on **Kubernetes using Minikube** with PostgreSQL as the database, environment variables via ConfigMaps & Secrets, and static file handling.

---

## 📁 Repositories Used

- 🔹 **Source Code & Dockerfile**: [Simple-Extraction-Demo-Track](https://github.com/varal-uae/Simple-Extraction-Demo-Track.git)
- 🔸 **Kubernetes YAML Files**: [Habot-project](https://github.com/Prasad-1703/Habot-project.git)

---

## ⚙️ Prerequisites

Ensure you have the following tools installed on your local machine:

- [Docker Desktop](https://www.docker.com/products/docker-desktop)
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
2. 🚢 Start Minikube
Start Minikube with Docker as the driver:

bash
Copy
Edit
minikube start --driver=docker
Enable the ingress controller:

bash
Copy
Edit
minikube addons enable ingress
3. 🐳 Build & Push Docker Image
You have two options:

🔹 Option 1: Use Prebuilt Image (Quick Start)
You can directly use the public image:

yaml
Copy
Edit
image: prasad1703/simpleextraction:latest
✅ Already referenced in the Kubernetes deployment.yaml file.

🔸 Option 2: Build Your Own Image
If you'd like to modify or build the app yourself:

bash
Copy
Edit
cd Simple-Extraction-Demo-Track
docker build -t yourdockerhubusername/simpleextraction:v1 .
docker push yourdockerhubusername/simpleextraction:v1
Then update your deployment.yaml:

yaml
Copy
Edit
image: yourdockerhubusername/simpleextraction:v1
4. 🔐 Create ConfigMap & Secrets
bash
Copy
Edit
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
5. 🗄 Deploy PostgreSQL
bash
Copy
Edit
kubectl apply -f postgres-deployment.yaml
kubectl apply -f postgres-service.yaml
6. 🛠 Deploy Django App
Apply deployment and service files:

bash
Copy
Edit
kubectl apply -f django-deployment.yaml
kubectl apply -f django-service.yaml
7. 🏁 Run Migrations Automatically (Init Container)
To automatically run migrations before the app starts, an init container is used in the deployment file. Make sure your django-deployment.yaml includes it:

yaml
Copy
Edit
initContainers:
  - name: migrate
    image: prasad1703/simpleextraction:latest
    command: ["python", "manage.py", "migrate"]
    envFrom:
      - configMapRef:
          name: django-config
      - secretRef:
          name: django-secret
You can find the complete deployment in the Habot-project repo.

8. 🌐 Set Up Ingress
bash
Copy
Edit
kubectl apply -f ingress.yaml
Add this line to your Windows hosts file (C:\Windows\System32\drivers\etc\hosts):

txt
Copy
Edit
192.168.49.2 demo.local
Replace 192.168.49.2 with the IP from:

bash
Copy
Edit
minikube ip
9. 🌍 Access the App
Start the tunnel to expose your services:

bash
Copy
Edit
minikube tunnel
Then, visit:

arduino
Copy
Edit
http://demo.local
If NodePort is used:

nginx
Copy
Edit
minikube service django
✅ Final Notes
Ensure PostgreSQL and Django containers are in Running state.

If using a new Postgres version, delete Minikube volumes to avoid version conflicts:

bash
Copy
Edit
minikube delete --all
Logs:

bash
Copy
Edit
kubectl logs <pod-name>
🙌 Credits
💻 App Source: varal-uae/Simple-Extraction-Demo-Track

🔧 Kubernetes Configs: Prasad-1703/Habot-project

