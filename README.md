Kubernetes Cross-Namespace Microservices Lab ☸️

📖 Overview

This project demonstrates a production-grade microservices architecture on a local Kubernetes cluster (Minikube). The primary goal was to engineer bidirectional communication between isolated services residing in different namespaces, utilizing the Sidecar Pattern for debugging and internal networking verification.

🏗️ Architecture

The cluster is logically divided into two isolated environments to mimic real-world segregation:

Namespace A (default): Hosts the Frontend Service (Nginx).

Namespace B (development): Hosts the Backend Service (Python).

Key Patterns Implemented

Logical Isolation: Using Kubernetes Namespaces to separate workloads.

Sidecar Pattern: Injecting a busybox container alongside application containers to provide network utilities (wget, nslookup) without bloating the main application image.

Service Discovery: Configuring ClusterIP Services to act as internal load balancers and provide stable DNS names.

🚀 Getting Started

Prerequisites

Minikube

Kubectl

Docker Desktop (or equivalent container runtime)

1. Setup the Cluster

Start your local Minikube cluster:

minikube start


2. Create the Namespaces

Ensure the development namespace exists:

kubectl apply -f manifests/01-namespace.yaml


3. Deploy the Microservices

Apply the deployment manifests. This creates the Pods with their main application containers and the busybox sidecars.

kubectl apply -f manifests/02-deployment-a.yaml
kubectl apply -f manifests/03-deployment-b.yaml


4. Deploy the Services ("The Receptionists")

Create the ClusterIP services to expose the pods internally.

kubectl apply -f manifests/04-service-a.yaml
kubectl apply -f manifests/05-service-b.yaml


🧪 Verification & Testing

Test 1: Frontend to Backend (Default -> Development)

To verify that the Nginx app can talk to the Python app, we exec into the sidecar in the default namespace:

# Get the pod name
kubectl get pods

# Exec into the debug sidecar
kubectl exec -it <node-app-pod-name> -c debug-sidecar -- /bin/sh

# Test connection using the fully qualified domain name (FQDN)
wget -O- [http://python-service.development](http://python-service.development)


Expected Result: A directory listing HTML response from the Python server.

Test 2: Backend to Frontend (Development -> Default)

To verify the reverse connection:

# Get the pod name in the dev namespace
kubectl get pods -n development

# Exec into the debug sidecar
kubectl exec -it <python-app-pod-name> -n development -c debug-sidecar -- /bin/sh

# Test connection back to default
wget -O- [http://nginx-service.default](http://nginx-service.default)


Expected Result: The "Welcome to nginx!" HTML page.

👤 Author

Viraj Mandlik