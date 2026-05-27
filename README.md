# 🚀 End-to-End GitOps CI/CD Pipeline Using Kubernetes, Helm, ArgoCD & GitHub Actions

This project demonstrates a complete beginner-friendly modern DevOps workflow using:

* Kubernetes (Kind Cluster)
* Docker
* GitHub Actions
* Helm
* ArgoCD
* GitOps Workflow
* GitHub Codespaces

The best part:
✅ No cloud VM required
✅ No Docker Desktop required
✅ No AWS/GCP/Azure required
✅ Entire setup runs inside GitHub Codespaces

---

# 📌 Architecture

```text
Developer Pushes Code
        ↓
GitHub Actions CI Pipeline
        ↓
Docker Image Build
        ↓
DockerHub Push
        ↓
Helm Charts
        ↓
ArgoCD Watches GitHub Repo
        ↓
Kubernetes Cluster Sync
        ↓
Automatic Deployment 🚀
```

---

# 📂 Project Structure

```text
k8s-demo/
│
├── .github/workflows/
│   └── ci-cd.yml
│
├── k8s-demo-chart/
│
├── Dockerfile
├── package.json
├── package-lock.json
├── server.js
└── README.md
```

---

# ⚙️ Technologies Used

* Kubernetes
* Kind
* Docker
* DockerHub
* GitHub Actions
* Helm
* ArgoCD
* GitOps
* Node.js
* GitHub Codespaces

---

# 🚀 STEP 1 — Create GitHub Repository

Create a GitHub repository:

```bash
k8s-demo
```

Clone repository:

```bash
git clone https://github.com/<YOUR_USERNAME>/k8s-demo.git
```

Enter repository:

```bash
cd k8s-demo
```

---

# 🚀 STEP 2 — Create Node.js Application

Initialize project:

```bash
npm init -y
```

Install express:

```bash
npm install express
```

Create application file:

```bash
touch server.js
```

Add code inside `server.js`:

```javascript
const express = require('express');

const app = express();

app.get('/', (req, res) => {
  res.send('Hello from Kubernetes + ArgoCD + GitOps!');
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

Update package.json scripts:

```json
"scripts": {
  "start": "node server.js"
}
```

Run application:

```bash
npm start
```

---

# 🚀 STEP 3 — Create Dockerfile

Create Dockerfile:

```bash
touch Dockerfile
```

Add:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

# 🚀 STEP 4 — Build Docker Image

Build image:

```bash
docker build -t k8s-demo:v1 .
```

Verify images:

```bash
docker images
```

Run container:

```bash
docker run -d -p 3000:3000 k8s-demo:v1
```

Verify:

```bash
docker ps
```

---

# 🚀 STEP 5 — Push Docker Image To DockerHub

Login:

```bash
docker login
```

Tag image:

```bash
docker tag k8s-demo:v1 <DOCKER_USERNAME>/k8s-demo:v1
```

Push image:

```bash
docker push <DOCKER_USERNAME>/k8s-demo:v1
```

---

# 🚀 STEP 6 — Install Kind Kubernetes Cluster

Install kind:

```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.24.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

Verify:

```bash
kind version
```

Create cluster:

```bash
kind create cluster
```

Verify:

```bash
kubectl get nodes
```

---

# 🚀 STEP 7 — Install Helm

Install Helm:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

Verify:

```bash
helm version
```

---

# 🚀 STEP 8 — Create Helm Chart

Create chart:

```bash
helm create k8s-demo-chart
```

Verify structure:

```bash
tree k8s-demo-chart
```

---

# 🚀 STEP 9 — Configure values.yaml

Open:

```text
k8s-demo-chart/values.yaml
```

Update:

```yaml
replicaCount: 2

image:
  repository: <DOCKER_USERNAME>/k8s-demo
  pullPolicy: IfNotPresent
  tag: latest
```

---

# 🚀 STEP 10 — Update Deployment Template

Open:

```text
k8s-demo-chart/templates/deployment.yaml
```

Ensure image section:

```yaml
image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

Delete default probes:

```yaml
livenessProbe:
readinessProbe:
```

because beginner Node.js app may fail probe checks.

---

# 🚀 STEP 11 — Deploy Using Helm

Install release:

```bash
helm install k8s-demo-release ./k8s-demo-chart
```

Verify:

```bash
helm list
```

Check pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get svc
```

---

# 🚀 STEP 12 — Create GitHub Actions Workflow

Create directories:

```bash
mkdir -p .github/workflows
```

Create workflow file:

```bash
touch .github/workflows/ci-cd.yml
```

Add workflow:

```yaml
name: CI-CD Pipeline

on:
  push:
    branches:
      - main

jobs:

  build-and-push:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout Source Code
        uses: actions/checkout@v4

      - name: Verify Repository Files
        run: ls -la

      - name: Set Up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login To DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build Docker Image
        run: |
          docker build -t ${{ secrets.DOCKER_USERNAME }}/k8s-demo:${{ github.sha }} .

      - name: Tag Latest Image
        run: |
          docker tag ${{ secrets.DOCKER_USERNAME }}/k8s-demo:${{ github.sha }} \
          ${{ secrets.DOCKER_USERNAME }}/k8s-demo:latest

      - name: Push SHA Tagged Image
        run: |
          docker push ${{ secrets.DOCKER_USERNAME }}/k8s-demo:${{ github.sha }}

      - name: Push Latest Image
        run: |
          docker push ${{ secrets.DOCKER_USERNAME }}/k8s-demo:latest
```

---

# 🚀 STEP 13 — Add GitHub Secrets

Go to:

```text
Repo → Settings → Secrets and Variables → Actions
```

Create:

```text
DOCKER_USERNAME
DOCKER_PASSWORD
```

---

# 🚀 STEP 14 — Push Code

```bash
git add .
```

```bash
git commit -m "Initial CI/CD setup"
```

```bash
git push origin main
```

Watch workflow in:

```text
GitHub Repo → Actions
```

---

# 🚀 STEP 15 — Install ArgoCD

Create namespace:

```bash
kubectl create namespace argocd
```

Install ArgoCD:

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.11.3/manifests/install.yaml
```

Verify pods:

```bash
kubectl get pods -n argocd
```

Wait until all are Running.

---

# 🚀 STEP 16 — Configure ArgoCD

Patch config:

```bash
kubectl patch configmap argocd-cmd-params-cm -n argocd --type merge -p '{"data":{"server.insecure":"true"}}'
```

Restart server:

```bash
kubectl rollout restart deployment argocd-server -n argocd
```

---

# 🚀 STEP 17 — Access ArgoCD UI

Port forward:

```bash
kubectl port-forward svc/argocd-server -n argocd 9090:80 --address 0.0.0.0
```

In GitHub Codespaces:

* Open PORTS tab
* Make port 9090 PUBLIC
* Open in Browser

---

# 🚀 STEP 18 — Get ArgoCD Password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

Login:

```text
Username: admin
Password: <OUTPUT>
```

---

# 🚀 STEP 19 — Create ArgoCD Application

Use YAML:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: k8s-demo
  namespace: argocd

spec:
  project: default

  source:
    repoURL: https://github.com/<YOUR_USERNAME>/k8s-demo.git
    targetRevision: main
    path: k8s-demo-chart

  destination:
    server: https://kubernetes.default.svc
    namespace: default

  syncPolicy:
    automated:
      prune: true
      selfHeal: true

    syncOptions:
      - CreateNamespace=true
```

---

# 🚀 STEP 20 — Verify GitOps Deployment

Check ArgoCD dashboard:

```text
Healthy + Synced
```

Check pods:

```bash
kubectl get pods
```

Watch live rollout:

```bash
kubectl get pods -w
```

---

# 🚀 STEP 21 — Test Automatic Deployment

Update server.js:

```javascript
res.send("Hello from ArgoCD Auto Deployment!");
```

Push changes:

```bash
git add .
```

```bash
git commit -m "Updated app message"
```

```bash
git push origin main
```

Watch:

1. GitHub Actions build image
2. DockerHub push image
3. ArgoCD detect changes
4. Kubernetes rollout new pods

---

# 📌 Key Concepts Learned

* Kubernetes Architecture
* Docker Containerization
* GitHub Actions CI/CD
* Helm Templating
* ArgoCD
* GitOps Workflow
* Rolling Deployments
* Self-Healing Infrastructure
* Kubernetes Services
* Declarative Infrastructure

---

# 🚀 Final Outcome

You now have:

✅ Complete CI Pipeline
✅ GitOps Continuous Deployment
✅ Kubernetes Cluster
✅ Helm-Based Deployments
✅ ArgoCD Synchronization
✅ Automated Docker Builds
✅ GitHub Actions Integration

without requiring any cloud server.

---

# 📌 Future Improvements

* Dynamic image tag updates
* Monitoring using Prometheus & Grafana
* Ingress controller
* HTTPS/TLS
* Terraform
* Cloud deployment (EKS/GKE/AKS)
* Multi-environment GitOps
* AI-powered remediation

---

# 📌 Author

Vikas Yatnalli

GitHub:
https://github.com/yatnallivikas
