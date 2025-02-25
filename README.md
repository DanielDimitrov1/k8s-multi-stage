# Kubernetes Multi-Environment Setup

This repository provides a complete Kubernetes setup with three separate environments: **Testing, Staging, and Production**. It includes Ingress rules, RBAC, deployments, autoscaling, TLS certificates, and persistent storage using Minikube and kubectl.

## Features
- **Three namespaces:** `testing`, `staging`, and `production`
- **RBAC setup:** Admin user (full permissions) and Dev user (no permissions initially)
- **Ingress controller:** NGINX Ingress with routing rules
- **Deployments:** One node running three pods, each with a single container
- **Autoscaling:** Horizontal Pod Autoscaler enabled
- **TLS security:** Certificates configured with cert-manager
- **Persistent Storage:** Local PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs)

## Setup Instructions

### 1. Start Minikube
```sh
minikube start --memory=4g --cpus=2
```
Check status:
```sh
minikube status
```

### 2. Create Namespaces
```sh
kubectl create namespace testing
kubectl create namespace staging
kubectl create namespace production
```

### 3. Configure RBAC
#### Admin User (Full Permissions)
```sh
kubectl apply -f admin-rbac.yaml
```
#### Dev User (No Permissions Initially)
```sh
kubectl apply -f dev-rbac.yaml
```

### 4. Enable Ingress Controller
```sh
minikube addons enable ingress
```

Create an Ingress rule:
```sh
kubectl apply -f ingress.yaml
```
Update `/etc/hosts`:
```sh
echo "$(minikube ip) myapp.local" | sudo tee -a /etc/hosts
```

### 5. Deploy Applications
```sh
kubectl apply -f deployment.yaml
```

### 6. Enable Horizontal Pod Autoscaling
```sh
kubectl autoscale deployment myapp --cpu-percent=50 --min=1 --max=5 -n staging
```
Check autoscaler:
```sh
kubectl get hpa -n staging
```

### 7. Configure Persistent Storage
```sh
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml -n staging
```

### 8. Install TLS Certificates
Install cert-manager:
```sh
kubectl apply -f https://github.com/jetstack/cert-manager/releases/latest/download/cert-manager.yaml
```
Create a self-signed certificate:
```sh
kubectl apply -f tls-secret.yaml -n staging
```

## Monitoring & Debugging
Check pods and deployments:
```sh
kubectl get pods -n staging -w
kubectl get deployments -n staging
```

Check logs:
```sh
kubectl logs -f deployment/myapp -n staging
```

Forward ports:
```sh
kubectl port-forward svc/myapp-service 8080:80 -n staging
```

## Next Steps
- Expand configurations for production and testing environments.
- Integrate Helm charts for better management.
- Add CI/CD automation.

This setup provides a solid Kubernetes infrastructure for multi-environment development. 🚀
