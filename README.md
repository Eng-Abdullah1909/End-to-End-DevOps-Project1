# DevSecOps CI/CD Pipeline with Jenkins and GitOps

## Overview

This repository contains a production-style DevSecOps CI/CD pipeline for a Node.js application using Jenkins.  
The pipeline automates testing, static code analysis, container image scanning, Docker build and push, and GitOps-based Kubernetes deployment.

The implementation demonstrates secure, automated, and traceable software delivery practices.

---

## CI/CD Workflow

1. Code is pushed to the application repository.
2. Jenkins pipeline is triggered automatically.
3. Dependencies are installed and audited.
4. Automated tests and coverage are executed.
5. Static code analysis is performed using SonarQube.
6. Docker image is built and tagged using the Git commit SHA.
7. Container image is scanned for vulnerabilities using Trivy.
8. Image is pushed securely to Docker Hub.
9. Kubernetes manifest in a separate GitOps repository is updated.
10. Changes are committed and pushed to the GitOps repository.
11. A GitOps controller (e.g., ArgoCD) deploys the updated version to Kubernetes.

---

## Pipeline Stages

### Dependency Management
- Install dependencies using `npm install`
- Fix known issues with `npm audit fix`
- Enforce critical vulnerability checks using `npm audit --audit-level=critical`

### Testing and Code Coverage
- Run automated tests
- Generate code coverage reports
- Publish coverage reports in Jenkins

### Static Application Security Testing (SAST)
- SonarQube scanner integration
- Quality Gate validation
- Build fails if quality standards are not met

### Containerization
- Build Docker image
- Tag image with Git commit SHA for traceability

### Container Vulnerability Scanning
- Scan image with Trivy
- Generate HTML vulnerability reports
- Separate low severity from high/critical findings

### Image Registry Integration
- Secure authentication using Jenkins credentials
- Push image to Docker Hub

### GitOps Deployment
- Clone or pull GitOps repository
- Update Kubernetes deployment manifest with new image tag
- Commit and push changes using secure token authentication

Deployment is handled by a GitOps controller monitoring the repository.

---

## Security Controls

- Secure credential management using Jenkins Credentials Store
- Static code analysis with enforced Quality Gates
- Dependency vulnerability auditing
- Container image vulnerability scanning
- Immutable image tagging using commit SHA
- GitOps-based deployment model (no direct cluster manipulation)

---

## Technologies Used

- Jenkins (Pipeline as Code)
- Node.js
- SonarQube
- Docker
- Trivy
- GitHub
- Kubernetes
- GitOps workflow
- ArgoCD

---

## Repository Contents

- `Jenkinsfile` – Complete CI/CD pipeline definition
- Application source code
- Dockerfile
- Test and coverage configuration

---

## Engineering Practices Demonstrated

- End-to-end CI/CD automation
- DevSecOps integration
- Secure secret handling
- Automated quality enforcement
- Immutable artifact versioning
- GitOps-based continuous delivery
- Separation of application and infrastructure repositories
