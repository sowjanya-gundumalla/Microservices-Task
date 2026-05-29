# Microservices-Task

A microservices-based Node.js application containerized using Docker and Docker Compose, and deployed on Kubernetes using Minikube.

## Architecture

| Service         | Port | Description                        |
|-----------------|------|------------------------------------|
| user-service    | 3000 | Handles user data                  |
| product-service | 3001 | Manages product listings           |
| order-service   | 3002 | Manages orders                     |
| gateway-service | 3003 | API Gateway routes to all services |

## Project Structure

```
Microservices-Task/
├── Microservices/
│   ├── user-service/
│   │   ├── app.js
│   │   ├── package.json
│   │   └── Dockerfile
│   ├── product-service/
│   │   ├── app.js
│   │   ├── package.json
│   │   └── Dockerfile
│   ├── order-service/
│   │   ├── app.js
│   │   ├── package.json
│   │   └── Dockerfile
│   ├── gateway-service/
│   │   ├── app.js
│   │   ├── package.json
│   │   └── Dockerfile
│   ├── docker-compose.yml
│   └── README.md
└── submission/
    ├── deployments/
    │   ├── user-service.yaml
    │   ├── product-service.yaml
    │   ├── order-service.yaml
    │   └── gateway-service.yaml
    ├── services/
    │   ├── user-service.yaml
    │   ├── product-service.yaml
    │   ├── order-service.yaml
    │   └── gateway-service.yaml
    └── README.md
```

---

## Part 1 - Docker and Docker Compose

### Prerequisites

- Docker v20 or above
- Docker Compose v2 or above

Verify installations:
```bash
docker --version
docker compose version
```

### Setup and Running

Clone the repository:
```bash
git clone <your-forked-repo-url>
cd Microservices-Task/Microservices
```

Build and start all services:
```bash
docker-compose up --build
```

Run in background:
```bash
docker-compose up --build -d
```

Stop all services:
```bash
docker-compose down
```

### Testing Each Service

```bash
curl http://localhost:3000/users
curl http://localhost:3001/products
curl http://localhost:3002/orders
curl http://localhost:3003/health
```

### Verifying Containers

```bash
docker ps
docker logs user-service
docker logs product-service
docker logs order-service
docker logs gateway-service
```

---

## Part 2 - Kubernetes Deployment using Minikube

### Prerequisites

- Docker v20 or above
- Minikube v1.38 or above
- kubectl

### Minikube Setup

Start Minikube:
```bash
minikube start --driver=docker
```

Verify cluster is running:
```bash
kubectl get nodes
```

Switch to Minikube's internal Docker:
```bash
eval $(minikube docker-env)
```

Build images inside Minikube's Docker:
```bash
cd Microservices-Task/Microservices
docker-compose build
```

Verify images are built:
```bash
docker images
```

### Deploying to Kubernetes

Apply all deployments:
```bash
kubectl apply -f submission/deployments/
```

Apply all services:
```bash
kubectl apply -f submission/services/
```

Verify pods are running:
```bash
kubectl get pods
```

Verify services are created:
```bash
kubectl get services
```

### Testing Services

Test using port-forward:
```bash
kubectl port-forward service/user-service 3000:3000 &
kubectl port-forward service/product-service 3001:3001 &
kubectl port-forward service/order-service 3002:3002 &
```

Then curl each service:
```bash
curl http://localhost:3000/health
curl http://localhost:3000/users
curl http://localhost:3001/health
curl http://localhost:3001/products
curl http://localhost:3002/health
curl http://localhost:3002/orders
```

Test gateway service:
```bash
minikube service gateway-service --url
curl <url-from-above>/health
```

### Troubleshooting

Pods in crash loop:
```bash
kubectl describe pod <pod-name>
```
Common cause: liveness probe path returning 404. Check app.js for valid health endpoint and update probe path in deployment yaml.

Images not found:
```bash
eval $(minikube docker-env)
docker-compose build
```
Always build images after switching to Minikube's Docker environment.

Apiserver not responding:
```bash
minikube delete
minikube start --driver=docker
```

Changes not reflecting:
```bash
kubectl apply -f deployments/
```

View logs of a service:
```bash
kubectl logs deployment/user-service
kubectl logs deployment/gateway-service
```

---

## Notes

- Entry point for all services is app.js
- imagePullPolicy is set to Never to use local images
- Liveness and readiness probes check /health endpoint
- ClusterIP is used for internal services, NodePort for gateway
- Containers communicate using service names as hostnames, not localhost