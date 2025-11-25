# Kubernetes Manifests

This directory contains all Kubernetes manifest files for deploying the voting application.

## Files

- `vote-deployment.yaml` - Vote service deployment and NodePort service (port 30000)
- `result-deployment.yaml` - Result service deployment and NodePort service (port 30001)
- `worker-deployment.yaml` - Worker service deployment
- `redis-deployment.yaml` - Redis deployment and ClusterIP service
- `db-deployment.yaml` - PostgreSQL deployment and ClusterIP service

## Deployment Order

Deploy in this order for best results:

```bash
# 1. Deploy data layer first
kubectl apply -f redis-deployment.yaml
kubectl apply -f db-deployment.yaml

# 2. Wait for data layer to be ready
kubectl wait --for=condition=ready pod -l app=redis --timeout=60s
kubectl wait --for=condition=ready pod -l app=db --timeout=60s

# 3. Deploy application layer
kubectl apply -f worker-deployment.yaml
kubectl apply -f vote-deployment.yaml
kubectl apply -f result-deployment.yaml
```

Or deploy all at once:

```bash
kubectl apply -f .
```

## Access the Application

- Vote: http://<node-ip>:30000
- Result: http://<node-ip>:30001

For Minikube:
```bash
minikube service vote
minikube service result
```

## Verify Deployment

```bash
kubectl get all
kubectl get pods
kubectl get services
```

## Clean Up

```bash
kubectl delete -f .
```
