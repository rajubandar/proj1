# proj1 — Static Nginx App on Kind (Kubernetes)

Deploy a simple Nginx static site on a local Kubernetes cluster using **Kind** (Kubernetes in Docker).

## What's inside

```
proj1/
├── app/
│   └── index.html          # Static site served by Nginx
├── k8s/
│   ├── deployment.yaml     # Nginx Deployment (2 replicas)
│   └── service.yaml        # NodePort Service to expose the app
├── kind-config.yaml        # Kind cluster config with port mapping
└── README.md
```

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

```bash
# Install Kind (Linux/macOS)
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
chmod +x ./kind && sudo mv ./kind /usr/local/bin/kind
```

---

## Step 1 — Create the Kind Cluster

```bash
kind create cluster --name nginx-cluster --config kind-config.yaml
```

Verify it's running:

```bash
kubectl cluster-info --context kind-nginx-cluster
kubectl get nodes
```

---

## Step 2 — Load the Nginx Image into Kind

Kind runs in Docker and can't pull from a local registry automatically, so we load the official image:

```bash
docker pull nginx:alpine
kind load docker-image nginx:alpine --name nginx-cluster
```

---

## Step 3 — Deploy the App

```bash
# Apply the Deployment
kubectl apply -f k8s/deployment.yaml

# Apply the Service
kubectl apply -f k8s/service.yaml
```

---

## Step 4 — Practice kubectl Commands

```bash
# List all pods
kubectl get pods

# Watch pods come up live
kubectl get pods -w

# Describe a pod (replace <pod-name> with actual name from get pods)
kubectl describe pod <pod-name>

# View logs of a pod
kubectl logs <pod-name>

# List services
kubectl get svc

# Describe the service
kubectl describe svc nginx-service
```

---

## Step 5 — Access the App

Because Kind maps `hostPort 30080 → NodePort 30080`, open your browser:

```
http://localhost:30080
```

Or test with curl:

```bash
curl http://localhost:30080
```

You should see the static HTML page served by Nginx.

---

## Step 6 — Cleanup

```bash
# Delete the deployment and service
kubectl delete -f k8s/

# Delete the Kind cluster entirely
kind delete cluster --name nginx-cluster
```

---

## Skills Gained

| Concept | What you practiced |
|---|---|
| Pod | Smallest deployable unit in K8s |
| Deployment | Manages replica sets, rolling updates |
| Service (NodePort) | Exposes app outside the cluster |
| YAML manifests | Declarative infrastructure |
| kubectl CLI | apply, get, describe, logs |
| Kind | Local K8s cluster in Docker |
