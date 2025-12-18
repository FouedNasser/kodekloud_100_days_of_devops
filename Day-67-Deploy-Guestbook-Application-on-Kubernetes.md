# Day 67: Deploy Guestbook Application on Kubernetes

## Challenge Description
The Nautilus Application development team required the deployment of a guestbook application on the Kubernetes cluster. The infrastructure consists of a multi-tier architecture:
- **Back-end Tier:** A Redis master (1 replica) and Redis slaves (2 replicas).
- **Front-end Tier:** A PHP-based frontend (3 replicas) exposed via a NodePort service.

The requirements included specific resource requests (CPU and Memory), environment variables, and specific container images/ports.

## Key Concepts
- **Multi-tier Architecture:** Separating the database (Redis) from the application (PHP) for better scalability and management.
- **Resource Limits & Requests:** Ensuring pods have the necessary resources (100m CPU, 100Mi Memory) while preventing resource exhaustion.
- **Service Discovery:** Using environment variables (`GET_HOSTS_FROM: dns`) to allow the frontend and slaves to find the Redis master via DNS.
- **NodePort Service:** Exposing the frontend externally on a specific port (30009).

## Solution

To access the Kubernetes cluster, we connect to the jump server:
```bash
ssh thor@jump_host.stratos.xfusioncorp.com
```

We created a manifest file named `guestbook.yaml` with the following configuration:

```yaml
# --- Redis Master ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis
    role: master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
      role: master
  template:
    metadata:
      labels:
        app: redis
        role: master
    spec:
      containers:
      - name: master-redis-devops
        image: redis
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
        ports:
        - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis
    role: master
spec:
  ports:
  - port: 6379
    targetPort: 6379
  selector:
    app: redis
    role: master
---
# --- Redis Slave ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
  labels:
    app: redis
    role: slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis
      role: slave
  template:
    metadata:
      labels:
        app: redis
        role: slave
    spec:
      containers:
      - name: slave-redis-devops
        image: gcr.io/google_samples/gb-redisslave:v3
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
        env:
        - name: GET_HOSTS_FROM
          value: "dns"
        ports:
        - containerPort: 6379
---
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis
    role: slave
spec:
  ports:
  - port: 6379
  selector:
    app: redis
    role: slave
---
# --- Frontend ---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: guestbook
      tier: frontend
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis-devops
        image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        resources:
          requests:
            cpu: "100m"
            memory: "100Mi"
        env:
        - name: GET_HOSTS_FROM
          value: "dns"
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  type: NodePort
  ports:
  - port: 80
    nodePort: 30009
  selector:
    app: guestbook
    tier: frontend
```

We applied the configuration using the following command:

```bash
kubectl apply -f guestbook.yaml
```

## Verification

We verified the deployment and services using:

```bash
# Check all resources
kubectl get all

# Verify the frontend service NodePort
kubectl get svc frontend

# Verify pod status and resource requests
kubectl describe pod <frontend-pod-name>
```
