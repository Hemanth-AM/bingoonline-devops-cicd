# Bingo Online - DevOps CI/CD Pipeline

## Overview

This project demonstrates an end-to-end DevOps CI/CD pipeline for a Node.js application.

The pipeline automates:

- Source code checkout
- Dependency installation
- Static code analysis
- Quality gate validation
- Dependency vulnerability scanning
- Filesystem security scanning
- Docker image creation
- Docker image vulnerability scanning
- Docker image publishing
- Application deployment

## DevOps Tools

| Tool | Purpose |
|------|---------|
| Git | Version Control |
| GitHub | Source Code Management |
| Jenkins | CI/CD Automation |
| SonarQube | Static Code Analysis |
| OWASP Dependency-Check | Dependency Vulnerability Scanning |
| Trivy | Security Scanning |
| Docker | Containerization |
| Docker Hub | Container Registry |
| Linux | CI/CD Environment |
<img width="957" height="509" alt="image" src="https://github.com/user-attachments/assets/ab395518-0e50-48d6-ac77-37055edd6368" />

## CI/CD Pipeline

GitHub
    |
    v
Jenkins
    |
    +----> SonarQube
    |
    +----> OWASP Dependency-Check
    |
    +----> Trivy FS Scan
    |
    v
Docker Build
    |
    v
Trivy Image Scan
    |
    v
Docker Hub
    |
    v
Docker Container
    |
    v
Application

## Pipeline Stages

1. Checkout
2. Install Dependencies
3. SonarQube Analysis
4. Quality Gate
5. OWASP Dependency Check
6. Trivy Filesystem Scan
7. Docker Build
8. Trivy Docker Image Scan
9. Docker Push
10. Deployment

## Security

Credentials are managed using Jenkins Credentials instead of storing secrets in the repository.

Examples:

- Docker Hub credentials
- SonarQube token
- NVD API key

No credentials are hardcoded in the Jenkinsfile.

## Docker

Build:

docker build -t bingoonlineproject:latest .

Run:

docker run -d --name bingoonline -p 3000:3000 bingoonlineproject:latest

## Future Improvements

- Add GitHub Webhook
- Add Kubernetes deployment
- Add Helm chart
- Add Prometheus monitoring
- Add Grafana dashboards
- Add centralized logging
- Add Kubernetes security scanning
- Implement blue-green or rolling deployment
