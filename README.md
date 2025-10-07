# docker-java-sample-webapp

Kubernetes + Docker + Maven example — deploy a Java WAR (Tomcat) to **Minikube** (or any k8s cluster).

## Overview
This project builds a Java webapp as a WAR, packages it into a Tomcat-based Docker image, and deploys it into Kubernetes.  
Key files in this repo:
- `Dockerfile` — multi-stage build (Maven → Tomcat).
- `pom.xml` — Maven project config (packaging = `war`).
- `namespace.yml` — Kubernetes Namespace.
- `deployment.yml` — Kubernetes Deployment (Tomcat image).
- `service.yml` — Kubernetes Service (NodePort).
- `README.md` — this file.

---

## Prerequisites
- Docker installed
- Minikube installed (or access to a Kubernetes cluster)
- kubectl installed
- Maven & JDK (optional — you can build inside Docker instead)

---

## Quick summary of your provided behavior
1. Build the WAR (Maven) and Docker image.
2. Start Minikube (you used Docker driver).
3. Load the image into Minikube (or build directly into Minikube's Docker daemon).
4. `kubectl apply` namespace, deployment, service.
5. Port-forwarded service to `8080:80`.

---

## Build & create image

```bash
# Build WAR locally
mvn clean package -DskipTests

# Build Docker image on host
docker build -t docker-java-sample-webapp:1.0 .

# Start minikube (if not started)
minikube start --driver=docker

# Load image into minikube so k8s node can use it
minikube image load docker-java-sample-webapp:1.0
```

## Deploy to Minikube
### Apply YAMLs in this order:
```bash
kubectl apply -f namespace.yml
kubectl apply -f deployment.yml -n java-webapp
kubectl apply -f service.yml -n java-webapp
```

## Access the application
### Option 1 — Port-forward (fast, local)
```bash
kubectl port-forward svc/webapp-service 8080:80 -n java-webapp
```

### Option 2 — minikube service (opens browser or prints URL)
```bash
minikube service webapp-service -n java-webapp --url
```

### Option 3 — NodePort + Minikube IP
```bash
kubectl get svc webapp-service -n java-webapp
minikube ip
```

## Cleanup
```bash
kubectl delete namespace java-webapp
minikube stop
minikube delete   # optional: remove cluster
```
