# TestApp

TestApp is a full-stack web application consisting of a backend, frontend, and Redis service, designed to demonstrate scalability, deployment, and management in a Kubernetes environment. This application is deployed using Helm charts and includes rolling updates and Horizontal Pod Autoscaling (HPA) for high availability and efficiency.

<div align="center">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/DS-DESbmZQ4?si=Wj0QSrf_FDv6wDyu" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</div>

---

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Deployment](#deployment)
  - [Kubernetes Setup](#kubernetes-setup)
  - [Helm Chart Deployment](#helm-chart-deployment)
  - [Horizontal Scaling and Rolling Updates](#horizontal-scaling-and-rolling-updates)
- [Configuration](#configuration)
- [Usage](#usage)
- [Health Checks](#health-checks)
- [CI/CD Pipeline](#cicd-pipeline)

---

## Overview

TestApp is designed to simulate a microservices architecture with the following components:-

- **Frontend**: A user interface interacting with the backend.
- **Backend**: A service that processes requests and communicates with Redis for caching.
- **Redis**: A caching service to store session or temporary data.

This application is containerized and managed using Kubernetes, with Helm charts for easier management and deployment. It is scalable using Horizontal Pod Autoscalers (HPA) and supports rolling updates to ensure minimal downtime during application updates.

---

## Architecture

The application consists of the following microservices:

- **Frontend Service**: A web application served on port `5175` (node port `31000`).
- **Backend Service**: A REST API served on port `8000`.
- **Redis Service**: A caching layer running on port `6379`.

These services communicate through internal Kubernetes DNS:

- The `frontend` communicates with the `backend` via `http://backend-svc:8000`.
- The `backend` communicates with `redis` via `redis://redis-svc:6379`.

---

## Prerequisites

Before deploying the application, make sure you have the following tools installed:

- **Kubernetes**: A running Kubernetes cluster (local or cloud).
- **kubectl**: Command-line tool for interacting with Kubernetes clusters.
- **Helm**: Package manager for Kubernetes.
- **Docker**: For building and pushing container images.

---

## Deployment

### Kubernetes Setup

Ensure that your Kubernetes cluster is up and running. You can use any cloud provider or a local Kubernetes setup like Minikube or Kind.

1. **Set up kubectl**: 
   ```bash
   kubectl config use-context <your-cluster-name>
   ```

2. **Verify the Kubernetes cluster**:
   ```bash
   kubectl get nodes
   ```

3. **Ensure Helm is initialized**:
   ```bash
   helm init
   ```

### Helm Chart Deployment

To deploy TestApp with Helm, follow these steps:

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd <project-directory>
   ```

2. **Install the Helm chart**:
   ```bash
   helm install testapp . --atomic --timeout 30s
   ```

This will deploy all the services in your Kubernetes cluster, including the frontend, backend, and Redis components.

### Horizontal Scaling and Rolling Updates

TestApp uses **HorizontalPodAutoscalers (HPA)** to automatically scale the number of pods based on CPU utilization.

- **Min Replicas**: 1
- **Max Replicas**: 5
- **Target CPU Utilization**: 70%

To check the scaling status, run:
```bash
kubectl get hpa
```

TestApp uses **RollingUpdate** strategy for deployments, meaning new pods are rolled out with minimal disruption.

---

## Configuration

The configuration for the application can be found in the `values.yaml` file.

- **Backend Configuration**:
  - `image`: Docker image for the backend service.
  - `servicePort`: The port the backend service listens on.

- **Frontend Configuration**:
  - `image`: Docker image for the frontend service.
  - `servicePort`: The port the frontend service listens on.
  - `nodePort`: The external node port exposed for frontend access.

- **Redis Configuration**:
  - `image`: Docker image for the Redis service.
  - `servicePort`: The port the Redis service listens on.

---

## Usage

Once the deployment is complete, access the frontend by visiting the NodePort:

```bash
http://<node-ip>:31000
```

The frontend will communicate with the backend via `http://backend-svc:8000`, and the backend will use Redis for caching.

---

## Health Checks

The application includes health checks for each service:

- **Frontend**: Accessible via the NodePort.
- **Backend**: Accessible via `http://backend-svc:8000`.
- **Redis**: Accessible via `redis://redis-svc:6379`.

---

## CI/CD Pipeline

This project includes integration with a CI/CD pipeline that automates the deployment process. The pipeline performs the following tasks:

1. **Build**: The Docker images for frontend, backend, and Redis are built.
2. **Push**: The images are pushed to a container registry.
3. **Deploy**: Helm deploys the latest changes to the Kubernetes cluster.

### CI/CD Tools Used:
- **GitHub Actions** (or any other CI/CD tool)


---

## Troubleshooting

### Common Issues:

1. **HPA not scaling pods**:
   - Check if the metrics server is running:
     ```bash
     kubectl get deployment metrics-server -n kube-system
     ```
   - Ensure the resource requests and limits are properly defined.

2. **Pods not starting**:
   - Check the pod logs for errors:
     ```bash
     kubectl logs <pod-name>
     ```

3. **Helm errors**:
   - If the release fails to install, try upgrading or uninstalling the release:
     ```bash
     helm upgrade testapp .
     helm uninstall testapp
     ```

---


---

## Special Features

### Horizontal Pod Autoscaling (HPA)

TestApp leverages Kubernetes' Horizontal Pod Autoscaler (HPA) to automatically adjust the number of running pods based on CPU utilization. This ensures that the application can handle varying loads efficiently.

### Rolling Updates

The deployment strategy for TestApp is set to **RollingUpdate**, which means that new versions of the application are rolled out gradually, ensuring minimal downtime. This is particularly useful for continuous integration and deployment (CI/CD) pipelines.

### Health Checks

Each service in TestApp includes health checks to ensure that the application is running correctly. The health checks are implemented using Kubernetes' liveness and readiness probes.

### CI/CD Integration

TestApp is integrated with a CI/CD pipeline that automates the build, test, and deployment processes. This ensures that new changes are deployed quickly and reliably.

### Resource Management

The application includes resource requests and limits for each service, ensuring that Kubernetes allocates the appropriate amount of resources to each pod. This helps in optimizing resource usage and preventing resource contention.

---

