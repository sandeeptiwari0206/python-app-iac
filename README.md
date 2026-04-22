<div align="center">

# 🏗️ Python App — Infrastructure as Code with AWS CloudFormation

### Full-stack Python application deployed on AWS using CloudFormation IaC + Jenkins CI/CD

[![Python](https://img.shields.io/badge/Python-27.5%25-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://github.com/sandeeptiwari0206/python-app-iac)
[![CloudFormation](https://img.shields.io/badge/AWS-CloudFormation-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com/cloudformation/)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?style=for-the-badge&logo=jenkins&logoColor=white)](https://www.jenkins.io/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-K8s-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)

</div>

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Project Structure](#-project-structure)
- [Tech Stack](#-tech-stack)
- [Infrastructure — CloudFormation](#-infrastructure--cloudformation)
- [CI/CD Pipeline — Jenkins](#-cicd-pipeline--jenkins)
- [Getting Started](#-getting-started)
- [Environment Variables & Secrets](#-environment-variables--secrets)
- [Docker & Compose](#-docker--compose)
- [Kubernetes](#-kubernetes)
- [Author](#-author)

---

## 📖 Overview

This project provisions a **production-grade Python web application** on AWS using **CloudFormation Infrastructure as Code** — defining all cloud resources (VPC, EC2, Security Groups, Auto Scaling) in repeatable, version-controlled YAML templates.

Unlike manual console-click provisioning, every resource in this stack is:
- ✅ **Reproducible** — same template, same infrastructure every time
- ✅ **Version controlled** — infra changes tracked in Git like application code
- ✅ **Automated** — Jenkins pipeline builds, pushes, and deploys on every commit
- ✅ **Rollback-safe** — CloudFormation supports one-command stack rollback

The application consists of a **Python backend API** and an **HTML/JS frontend**, both containerised with Docker and orchestrated via Docker Compose on an EC2 instance provisioned by CloudFormation.

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     GitHub Repository                        │
│                           │                                 │
│               Push to main branch                           │
└───────────────────────────┼─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Jenkins Pipeline                          │
│                                                             │
│  ┌──────────────┐   ┌───────────────┐   ┌───────────────┐  │
│  │  Checkout &  │──►│  Build & Push │──►│  Deploy on    │  │
│  │  Build Code  │   │  to DockerHub │   │  EC2 via SSH  │  │
│  └──────────────┘   └───────────────┘   └───────────────┘  │
│                                                  │          │
│                                    ┌─────────────▼──────┐  │
│                                    │  Cleanup Old Images │  │
│                                    └────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              AWS Infrastructure (CloudFormation)             │
│                                                             │
│  ┌──────┐   ┌────────┐   ┌──────────────────────────────┐  │
│  │  VPC │──►│ Subnet │──►│        EC2 Instance           │  │
│  └──────┘   └────────┘   │                              │  │
│                           │  ┌────────────┐  ┌────────┐ │  │
│  ┌──────────────────┐     │  │  Backend   │  │Frontend│ │  │
│  │  Security Group  │────►│  │  :8000     │◄─│  :80   │ │  │
│  │  (80, 8000, 22)  │     │  └────────────┘  └────────┘ │  │
│  └──────────────────┘     └──────────────────────────────┘  │
│                                                             │
│  ┌──────────────────────┐   ┌─────────────────────────────┐ │
│  │  Internet Gateway    │   │  Auto Scaling Group         │ │
│  └──────────────────────┘   └─────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
python-app-iac/
│
├── backend/                    # Python REST API
│   ├── app.py                  # Flask / FastAPI application
│   ├── requirements.txt
│   └── Dockerfile
│
├── frontend/                   # HTML / JavaScript frontend (Nginx)
│   ├── index.html
│   ├── app.js
│   └── Dockerfile
│
├── infra/                      # AWS CloudFormation templates
│   ├── vpc.yaml                # VPC, Subnets, Internet Gateway
│   ├── ec2.yaml                # EC2 instance, Security Groups
│   ├── autoscaling.yaml        # Auto Scaling Group & Launch Template
│   └── main.yaml               # Root stack (nested stacks)
│
├── k8s/                        # Kubernetes manifests (optional)
│   ├── backend-deployment.yaml
│   ├── frontend-deployment.yaml
│   └── service.yaml
│
├── .github/workflows/          # GitHub Actions workflows
│
├── Jenkinsfile                 # Jenkins declarative CI/CD pipeline
├── docker-compose.yml          # Multi-service container orchestration
└── .gitignore
```

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|-----------|
| **Language** | Python, HTML, JavaScript |
| **IaC Tool** | AWS CloudFormation (YAML) |
| **Cloud** | AWS (EC2, VPC, Security Groups, Auto Scaling) |
| **Containers** | Docker, Docker Compose |
| **CI/CD** | Jenkins (Declarative Pipeline) |
| **Container Registry** | Docker Hub |
| **Orchestration** | Kubernetes (optional) |
| **Web Server** | Nginx |

---

## ☁️ Infrastructure — CloudFormation

The `infra/` directory contains all AWS CloudFormation YAML templates for provisioning a fully reproducible environment.

### Resources Provisioned

| Resource | Description |
|----------|-------------|
| **VPC** | Custom Virtual Private Cloud with CIDR block |
| **Public Subnet** | Subnet in a chosen availability zone |
| **Internet Gateway** | Enables internet traffic in/out of the VPC |
| **Route Table** | Routes public traffic through the IGW |
| **Security Group** | Allows HTTP `:80`, API `:8000`, SSH `:22` |
| **EC2 Instance** | Hosts the application via Docker Compose |
| **Auto Scaling Group** | Scales EC2 capacity based on demand |
| **Launch Template** | Defines AMI, instance type, user data bootstrap |

### Deploy the Stack

```bash
# Configure AWS credentials
aws configure

# Deploy the CloudFormation stack
aws cloudformation deploy \
  --template-file infra/main.yaml \
  --stack-name python-app-stack \
  --capabilities CAPABILITY_IAM \
  --parameter-overrides \
      InstanceType=t3.small \
      KeyName=your-key-pair

# Check stack status
aws cloudformation describe-stacks \
  --stack-name python-app-stack \
  --query "Stacks[0].StackStatus"
```

### Tear Down

```bash
aws cloudformation delete-stack --stack-name python-app-stack
```

### Update Existing Stack

```bash
aws cloudformation deploy \
  --template-file infra/main.yaml \
  --stack-name python-app-stack \
  --capabilities CAPABILITY_IAM
```

---

## 🔄 CI/CD Pipeline — Jenkins

The Jenkins declarative pipeline automates the full build → push → deploy cycle across two agents.

### Pipeline Flow

```
Push to main
     │
     ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 1: Checkout, Build & Push  (built-in node)           │
│  ──────────────────────────────────────────────────────     │
│  • checkout scm                                             │
│  • docker build backend  → python-backend:<BUILD_NUMBER>    │
│  • docker build frontend → python-frontend:<BUILD_NUMBER>   │
│  • docker login DockerHub  (credential: dockerhub-pass)     │
│  • docker push both images tagged with BUILD_NUMBER         │
│  • docker logout                                            │
└─────────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 2: Deploy on EC2  (ec2 agent)                        │
│  ──────────────────────────────────────────────────────     │
│  • docker pull backend:<BUILD_NUMBER>                       │
│  • docker pull frontend:<BUILD_NUMBER>                      │
│  • export IMAGE_TAG=<BUILD_NUMBER>                          │
│  • docker compose down                                      │
│  • docker compose up -d                                     │
└─────────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────────┐
│  Stage 3: Cleanup  (ec2 agent)                              │
│  ──────────────────────────────────────────────────────     │
│  • docker image prune -af   (remove stale images)           │
│  • docker system df         (log disk usage)                │
└─────────────────────────────────────────────────────────────┘
```

### Jenkins Setup Requirements

| Requirement | Details |
|-------------|---------|
| Agent labels | `built-in`, `ec2` |
| Credential ID | `dockerhub-pass` (Secret Text — Docker Hub token) |
| Docker | Installed on both agents |
| EC2 SSH access | Configured in Jenkins node settings |

---

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Docker & Docker Compose
- AWS CLI configured (`aws configure`)
- Jenkins server (for CI/CD)

### 1. Clone the Repository

```bash
git clone https://github.com/sandeeptiwari0206/python-app-iac.git
cd python-app-iac
```

### 2. Provision AWS Infrastructure

```bash
aws cloudformation deploy \
  --template-file infra/main.yaml \
  --stack-name python-app-stack \
  --capabilities CAPABILITY_IAM
```

### 3. Run Locally with Docker Compose

```bash
IMAGE_TAG=local docker compose up --build

# Backend:  http://localhost:8000
# Frontend: http://localhost:80
```

### 4. Trigger CI/CD

Push to the `main` branch — Jenkins picks it up automatically and runs all 3 pipeline stages.

---

## 🔐 Environment Variables & Secrets

| Variable | Where | Description |
|----------|-------|-------------|
| `IMAGE_TAG` | `docker-compose.yml` | Docker image tag set by Jenkins `BUILD_NUMBER` |
| `ENV` | Backend container | Runtime environment (`production`) |
| `dockerhub-pass` | Jenkins Credentials Store | Docker Hub access token |
| `AWS_ACCESS_KEY_ID` | AWS CLI / CloudFormation | AWS credentials |
| `AWS_SECRET_ACCESS_KEY` | AWS CLI / CloudFormation | AWS credentials |

> ⚠️ Never commit secrets to the repository. Use the Jenkins credentials store or AWS Secrets Manager.

---

## 🐳 Docker & Compose

### Services

```yaml
services:
  backend:
    image: sandeeptiwari0206/python-backend:${IMAGE_TAG}
    container_name: python-backend
    restart: always
    ports: ["8000:8000"]
    environment: [ENV=production]
    networks: [python-net]

  frontend:
    image: sandeeptiwari0206/python-frontend:${IMAGE_TAG}
    container_name: python-frontend
    restart: always
    ports: ["80:80"]
    depends_on: [backend]
    networks: [python-net]

networks:
  python-net:
    driver: bridge
```

### Docker Hub Images

| Image | Tag Strategy |
|-------|-------------|
| `sandeeptiwari0206/python-backend` | Tagged with Jenkins `BUILD_NUMBER` |
| `sandeeptiwari0206/python-frontend` | Tagged with Jenkins `BUILD_NUMBER` |

---

## ☸️ Kubernetes

Kubernetes manifests are available in the `k8s/` directory for deploying to any cluster (EKS, GKE, etc.).

```bash
# Apply all manifests
kubectl apply -f k8s/

# Verify pods are running
kubectl get pods
kubectl get services

# View logs
kubectl logs -f deployment/python-backend
```

---

## 👨‍💻 Author

<div align="center">

**Sandeep Tiwari** — Cloud Engineer & DevOps Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/sandeep-tiwari-616a33116/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/sandeeptiwari0206)
[![Portfolio](https://img.shields.io/badge/Portfolio-Visit-3b82f6?style=flat-square)](https://your-portfolio-url.com)

📍 Jaipur, Rajasthan, India

</div>

---

<div align="center">

⭐ **If this project helped you, give it a star!**

</div>
