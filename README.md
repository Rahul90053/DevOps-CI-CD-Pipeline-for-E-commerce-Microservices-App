# DevOps-CI-CD-Pipeline-for-E-commerce-Microservices-App
Build and deploy a sample e-commerce app (3 microservices: product, cart, and user) using full DevOps practices on AWS.
# ----------------------------
# 1. Project Structure
# ----------------------------
# Root Folder Structure
# devops-ecommerce/
# ├── terraform/            # Infra as code for AWS
# ├── helm-charts/          # Helm charts for K8s deployment
# ├── user-service/         # User microservice
# ├── product-service/      # Product microservice
# ├── cart-service/         # Cart microservice
# ├── github-actions/       # CI/CD workflows
# └── README.md             # Project overview and instructions

# ----------------------------
# 2. Dockerfile (Example for user-service)
# ----------------------------
# File: user-service/Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]

# ----------------------------
# 3. Kubernetes Deployment (user-service)
# ----------------------------
# File: helm-charts/user-service/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
        - name: user-service
          image: <your-dockerhub-username>/user-service:latest
          ports:
            - containerPort: 3000

# ----------------------------
# 4. GitHub Actions CI/CD
# ----------------------------
# File: github-actions/deploy.yml
name: CI/CD Pipeline
on:
  push:
    branches: [ main ]
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        run: |
          docker build -t <your-dockerhub-username>/user-service:latest ./user-service
          docker push <your-dockerhub-username>/user-service:latest

      - name: Setup kubectl
        uses: azure/setup-kubectl@v3
        with:
          version: 'latest'

      - name: Deploy to Kubernetes
        run: |
          kubectl apply -f helm-charts/user-service/templates/deployment.yaml

# ----------------------------
# 5. Terraform to provision EKS (Simplified Example)
# ----------------------------
# File: terraform/main.tf
provider "aws" {
  region = "us-east-1"
}

resource "aws_eks_cluster" "demo" {
  name     = "demo-eks-cluster"
  role_arn = aws_iam_role.eks_role.arn
  # More config here...
}

# ----------------------------
# 6. README.md (Overview)
# ----------------------------
# File: README.md
# DevOps E-commerce Microservices Project
This project demonstrates an end-to-end CI/CD pipeline deploying 3 microservices to AWS EKS using GitHub Actions, Docker, Kubernetes, Helm, and Terraform.

## Features
- Microservices: user, product, cart
- Dockerized Services
- GitHub Actions CI/CD
- Kubernetes with Helm
- AWS Infrastructure via Terraform
- Monitoring (Prometheus + Grafana)

## To Do
- Add Helm charts for product and cart
- Add Terraform modules for full AWS infra
- Setup Prometheus/Grafana monitoring
- Record YouTube walkthrough 🚀

