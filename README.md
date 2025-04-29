# Kind Cluster with Multiple Laravel Applications

This project sets up a local Kubernetes cluster using Kind with two Laravel applications running on PHP 8.3 with Nginx.

## Prerequisites

- Docker
- Kind (Kubernetes IN Docker)
- kubectl
- Laravel applications in `app1` and `app2` directories

## Setup Instructions

1. Create the Kind cluster:
```bash
kind create cluster --config kind-config.yaml
```

kubectl cluster-info --context kind-kind

### Add secrets
kubectl create secret generic laravel-app1-secrets --from-literal=app-key=$(php artisan key:generate --show) -n laravel-apps

kubectl create secret generic laravel-app2-secrets --from-literal=app-key=$(openssl rand -base64 32) -n laravel-apps && kubectl create secret generic postgres-secret --from-literal=password=postgres -n laravel-apps


2. Build Docker images for both applications:
```bash
# Build app1
cd app1 && docker build -t laravel-app1:latest .

# Build app2
cd app2 && docker build -t laravel-app2:latest .
```

3. Load the images into Kind:
```bash
kind load docker-image laravel-app1:latest
kind load docker-image laravel-app2:latest
```

kubectl create namespace laravel-apps

4. Apply Kubernetes manifests:
```bash
kubectl apply -f k8s/
```

kubectl port-forward -n laravel-apps svc/postgres 5432:5432

5. Add local DNS entries (add to /etc/hosts):
```
127.0.0.1 app1.local
127.0.0.1 app2.local
```

## Accessing the Applications

- App1: http://app1.local
- App2: http://app2.local

## Application Stack

- PHP 8.3 with FPM
- Nginx web server
- Laravel framework

## Useful Commands

- Check cluster status:
```bash
kubectl get nodes
```

- Check pods:
```bash
kubectl get pods -n laravel-apps
```

- Check services:
```bash
kubectl get services -n laravel-apps
```

- Check ingress:
```bash
kubectl get ingress -n laravel-apps

kubectl get pods -n ingress-nginx
```

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml

### Wait for the ingress controller to be ready
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=120s


kubectl apply -f k8s/ingress.yaml

### Check where the laravel apps are running
kubectl get service -n ingress-nginx ingress-nginx-controller

## Cleanup

To delete the cluster:
```bash
kind delete cluster
``` 

## Updates the apps
cd app1 && docker build -t laravel-app1:latest . && kind load docker-image laravel-app1:latest && cd ../app2 && docker build -t laravel-app2:latest . && kind load docker-image laravel-app2:latest

cd .. && kubectl rollout restart deployment laravel-app1 laravel-app2 -n laravel-apps

### check the rollout restart status
kubectl rollout status deployment laravel-app1 laravel-app2 -n laravel-apps

### Get Postgres
kubectl get pods -n laravel-apps | grep postgres
kubectl exec -it -n laravel-apps postgres-56bdf84fc9-l9xgg -- psql -U postgres -d laravel




### UPDATES 2:41PM
