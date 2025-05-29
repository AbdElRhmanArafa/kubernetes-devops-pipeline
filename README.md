# Kubernetes DevOps Pipeline

A complete CI/CD pipeline setup for Kubernetes environments featuring Jenkins and SonarQube, designed to automate the build, test, analysis, and deployment of applications.

![Pipeline Overview](https://raw.githubusercontent.com/AbdElRhmanArafa/kubernetes-devops-pipeline/main/images/sonarqube_labeled.png)

## Table of Contents

- [Overview](#overview)
- [Components](#components)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Set Up Kubernetes Cluster](#set-up-kubernetes-cluster)
  - [Install Jenkins](#install-jenkins)
  - [Install SonarQube](#install-sonarqube)
- [Pipeline Configuration](#pipeline-configuration)
- [Pipeline Workflow](#pipeline-workflow)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## Overview

This project provides a complete DevOps pipeline for Kubernetes environments. It automates the process of building, testing, analyzing, and deploying applications using Jenkins and SonarQube.

## Components

- **Jenkins**: CI/CD server for automating build, test, and deployment
- **SonarQube**: Code quality and security analysis platform
- **Docker**: Container platform for packaging applications
- **Kubernetes**: Container orchestration platform

## Prerequisites

- Kubernetes cluster (v1.18+)
- Helm v3+
- `kubectl` CLI
- Docker Hub account (or another container registry)

## Installation

### Set Up Kubernetes Cluster

1. You can use the [k8s-cluster-automation repository](https://github.com/AbdElRhmanArafa/k8s-cluster-automation) to set up an easy Kubernetes cluster using Vagrant and Ansible, or use your own Kubernetes cluster.

### Install Jenkins

1. Create a namespace for DevOps tools:

   ```bash
   kubectl create namespace devops-tools
   ```

2. Deploy Jenkins to your Kubernetes cluster:

   ```bash
   kubectl apply -f jenkins/jenkins-01-serviceAccount.yaml -n devops-tools
   kubectl apply -f jenkins/jenkins-02-volume.yaml -n devops-tools
   kubectl apply -f jenkins/jenkins-03-deployment.yaml -n devops-tools
   kubectl apply -f jenkins/jenkins-04-service.yaml -n devops-tools
   ```

3. Verify Jenkins is running:

   ```bash
   kubectl get pods -n devops-tools
   ```

4. Access the Jenkins web interface:

   ```bash
   kubectl port-forward svc/jenkins -n devops-tools 8080:8080
   ```

   Then open your browser and visit `http://localhost:8080`

5. Retrieve the initial admin password:

   ```bash
   kubectl exec -n devops-tools $(kubectl get pods -n devops-tools -l app=jenkins-server -o jsonpath='{.items[0].metadata.name}') -- cat /var/jenkins_home/secrets/initialAdminPassword
   ```

6. Complete Jenkins setup by installing recommended plugins and creating an admin user.

### Install SonarQube

1. Create a namespace for SonarQube:

   ```bash
   kubectl create namespace sonarqube
   ```

2. Label a node for SonarQube (optional but recommended for production):

   ```bash
   kubectl label nodes <node-name> sonarqube=true
   ```

3. Deploy SonarQube using Helm:

   ```bash
   helm repo add sonarqube https://SonarSource.github.io/helm-chart-sonarqube
   helm repo update
   helm upgrade --install -n sonarqube --version '~8' sonarqube sonarqube/sonarqube -f sonarqube/values.yaml
   ```

## Pipeline Configuration

The repository includes two Jenkins pipeline files:

1. **Jenkinsfile-ci-sonarqube**: Performs code checkout, build, static code analysis with SonarQube, and artifact creation.
2. **jenkinsfile-Dockerize**: Takes the artifact from the previous pipeline, builds a Docker image, and pushes it to Docker Hub.

### Jenkins Configuration

After setting up Jenkins, you'll need to configure:

1. **Jenkins Credentials**:
   - Docker Hub credentials
   - Git repository access
   - SonarQube token

2. **Jenkins Plugins**:
   - SonarQube Scanner
   - Docker Pipeline
   - Kubernetes plugin

3. **Create Jenkins Pipelines**:
   - Create a new pipeline job named "ci-sonarqube" using the `Jenkinsfile-ci-sonarqube`
   - Create a new pipeline job named "Dockerize" using the `jenkinsfile-Dockerize`

4. **Configure SonarQube Connection in Jenkins**:
   - Go to Manage Jenkins > Configure System
   - Find the SonarQube servers section
   - Add a SonarQube instance with name 'sq1'

## Pipeline Workflow

1. **CI Pipeline**:
   - Code checkout from Git
   - Maven build
   - SonarQube analysis
   - Quality gate check
   - Artifact creation

2. **Docker Pipeline**:
   - Download artifact from the CI pipeline
   - Build Docker image
   - Push to Docker Hub

## Troubleshooting

- **Jenkins Pod Not Starting**: Check persistent volume claims and storage class
- **SonarQube Analysis Failures**: Verify SonarQube token and connection in Jenkins
- **Docker Push Failures**: Check Docker Hub credentials in Jenkins
