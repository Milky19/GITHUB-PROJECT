# DevSecOps Pipeline End-to-End Project 

**Developed by:** **Krishna – DevOps Engineer**

## Project Overview

This project demonstrates a complete **DevSecOps CI/CD Pipeline** by integrating security into every stage of the software delivery lifecycle.

The application used in this project is **PageTurn**, a deliberately vulnerable online bookstore designed for learning and demonstrating DevSecOps concepts. The vulnerabilities are intentionally included so that security tools can detect and report them during the CI/CD pipeline.

> **Important:** This project is intended **only for educational and demonstration purposes**. Never deploy this vulnerable application in a production environment.

---

# Project Structure

```text
.
├── app/
│   ├── server.js
│   ├── db.js
│   ├── package.json
│   ├── .env.example
│   └── .gitleaks.toml
│
├── frontend/
│   ├── src/
│   ├── package.json
│   └── vite.config.js
│
├── terraform-ec2/
├── jenkins/
├── .github/workflows/
├── docs/
├── fixes/
├── Dockerfile
├── docker-compose.yml
├── sonar-project.properties
└── README.md
```

---

# Project Description

PageTurn is a sample online bookstore built using:

* React.js Frontend
* Node.js & Express Backend
* Docker
* Terraform
* GitHub Actions
* Jenkins
* AWS EC2

The application contains intentionally seeded security vulnerabilities that allow DevSecOps tools to detect real-world security issues during the CI/CD pipeline.

---

# Technologies Used

* Git & GitHub
* GitHub Actions
* Jenkins
* Docker
* Docker Compose
* Node.js
* React.js (Vite)
* Terraform
* AWS EC2
* SonarQube
* Gitleaks
* Trivy
* Checkov
* OWASP ZAP

---

# Seeded Vulnerabilities

The application contains the following intentionally vulnerable features.

| Application Feature   | Vulnerability                  |
| --------------------- | ------------------------------ |
| Book Search           | SQL Injection                  |
| Login Page            | Authentication Bypass          |
| Customer Account      | IDOR                           |
| Book Reviews          | Stored XSS                     |
| Supplier Connectivity | Command Injection              |
| Email Preview         | Server-Side Template Injection |

These vulnerabilities help demonstrate how security scanners identify weaknesses during a DevSecOps pipeline.

---

# DevSecOps CI/CD Pipeline

The pipeline performs security checks before deploying the application.

```
Developer Push
        │
        ▼
GitHub Actions
        │
        ▼
Gitleaks
        │
        ▼
SonarQube
        │
        ▼
Trivy File System Scan
        │
        ▼
Checkov
        │
        ▼
Docker Build
        │
        ▼
Trivy Image Scan
        │
        ▼
Deploy to AWS EC2
        │
        ▼
OWASP ZAP
```

---

# Security Tools Used

## 1. Gitleaks

Detects:

* Hardcoded passwords
* API Keys
* AWS Credentials
* Secrets

---

## 2. SonarQube

Performs Static Application Security Testing (SAST).

Detects:

* SQL Injection
* Code Smells
* Bugs
* Security Hotspots
* Vulnerabilities

---

## 3. Trivy File System Scan

Scans:

* npm Dependencies
* Operating System Packages
* Known CVEs

---

## 4. Checkov

Scans Terraform Infrastructure as Code.

Detects:

* Misconfigured AWS Resources
* Public Security Groups
* IAM Risks
* Encryption Issues

---

## 5. Docker Build

Creates a production-ready Docker image for deployment.

---

## 6. Trivy Image Scan

Scans Docker images for:

* Critical CVEs
* High Severity Vulnerabilities
* Medium Severity Vulnerabilities
* Low Severity Vulnerabilities

---

## 7. Deploy to AWS EC2

Deploys the application automatically after all security stages complete successfully.

---

## 8. OWASP ZAP

Performs Dynamic Application Security Testing (DAST).

Detects:

* Cross-Site Scripting (XSS)
* SQL Injection
* Missing Security Headers
* Session Management Issues
* Authentication Problems

---

# Run the Application

## Backend

```bash
cd app
cp .env.example .env
npm install
npm start
```

---

## Frontend

```bash
cd frontend
npm install
npm run dev
```

Application URL:

```
http://localhost:5173
```

---

# Production Mode

```bash
cd frontend
npm install
npm run build

cd ../app
npm install
npm start
```

Application URL:

```
http://localhost:3000
```

---

# Run Using Docker

```bash
docker compose up --build
```

Application URL:

```
http://localhost:3000
```

---

# Deploy Infrastructure Using Terraform

```bash
cd terraform-ec2

cp terraform.tfvars.example terraform.tfvars

terraform init

terraform plan

terraform apply
```

---

# Run Security Scans

## Gitleaks

```bash
gitleaks detect --source . --config app/.gitleaks.toml
```

---

## SonarQube

```bash
export SONAR_TOKEN=<your-token>

export SONAR_HOST_URL=http://localhost:9000

sonar-scanner
```

---

## Trivy

```bash
trivy fs .
```

---

## Checkov

```bash
checkov -d terraform-ec2
```

---

# Learning Objectives

After completing this project, you will understand how to:

* Build a complete DevSecOps CI/CD pipeline
* Secure software development workflows
* Integrate automated security scanning
* Scan source code and dependencies
* Scan Docker images
* Scan Terraform Infrastructure as Code
* Perform Dynamic Application Security Testing
* Deploy applications securely on AWS EC2
* Automate security testing with GitHub Actions and Jenkins

---

# Future Enhancements

In the next phase of this project, the application can be extended to:

* Deploy on AWS EKS
* Implement Kubernetes
* Integrate Helm
* Use Argo CD for GitOps
* Add Prometheus Monitoring
* Add Grafana Dashboards
* Integrate AI-assisted vulnerability analysis
* Automate remediation through pull requests

---

# Author

**Krishna**
**DevOps Engineer**

**Skills**

* AWS Cloud
* Linux
* Git & GitHub
* GitHub Actions
* Jenkins
* Docker
* Kubernetes
* Terraform
* SonarQube
* Trivy
* Checkov
* OWASP ZAP
* DevSecOps
* CI/CD Automation

---

