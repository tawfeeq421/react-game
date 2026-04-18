# 🚀 Game App CI/CD Pipeline (Jenkins + Docker + EKS)

## 📌 Project Overview

This project demonstrates a complete **CI/CD pipeline** for a React-based game application using:

* Jenkins (CI/CD automation)
* Docker (containerization)
* Kubernetes (EKS deployment)
* SonarQube (code quality)
* Trivy (security scanning)

---

## 🛠️ Tech Stack

* Frontend: React (Node.js)
* CI/CD: Jenkins
* Containerization: Docker
* Orchestration: Kubernetes (AWS EKS)
* Code Quality: SonarQube
* Security: Trivy
* Cloud: AWS

---

## 🔄 CI/CD Pipeline Flow

1. Code pushed to GitHub
2. Jenkins pipeline triggered automatically
3. Install dependencies (npm install)
4. Run tests (Jest)
5. SonarQube code analysis
6. Quality Gate validation
7. Trivy filesystem scan
8. Docker image build & push to DockerHub
9. Trivy image scan
10. Deploy to AWS EKS
11. Rolling update using Kubernetes

---

## 📂 Project Structure

```
.
├── k8s/
│   ├── namespace.yml
│   ├── game-app-deployment.yml
│   ├── game-app-service.yml
│   └── ingress.yml
├── Jenkinsfile
├── package.json
└── src/
```

---

## ⚙️ Prerequisites

Before running this project, ensure you have:

* Jenkins installed with required plugins
* Docker installed
* AWS CLI configured
* kubectl installed
* eksctl installed
* Trivy installed
* SonarQube server configured

---

## 🔐 Jenkins Credentials Required

| ID          | Purpose          |
| ----------- | ---------------- |
| docker-cred | DockerHub login  |
| aws-creds   | AWS access keys  |
| sonarserver | SonarQube server |

---

## 🚀 Deployment Steps

1. Clone repository:

```
git clone https://github.com/tawfeeq421/react-game.git
```

2. Configure Jenkins pipeline

3. Run the pipeline

4. Access application via LoadBalancer / Ingress

---

## 🐳 Docker Image

```
tawfeeq421/game:<BUILD_NUMBER>
```

---

## ☸️ Kubernetes Deployment

* Deployment: game-app-deployment
* Namespace: game
* Service: NodePort / LoadBalancer
* Ingress: Enabled

---

## 🔍 Security Scanning

* Trivy FS Scan (source code)
* Trivy Image Scan (Docker image)

---

## 📊 Monitoring (Future Scope)

* Prometheus
* Grafana

---

## 🧠 Key Features

* Automated CI/CD pipeline
* Rolling updates in Kubernetes
* Integrated security scanning
* Code quality enforcement
* Scalable cloud deployment

---

## 🙌 Author

**Tawfeeq Ahmed**

---

## ⭐ If you like this project

Give it a star on GitHub ⭐
