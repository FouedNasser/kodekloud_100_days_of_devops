# Day 66: Deploy MySQL on Kubernetes

## Challenge Description
The Nautilus DevOps team requested a new MySQL server deployment on the Kubernetes cluster. The requirements included setting up persistent storage, managing sensitive data with secrets, and exposing the database service. Specifically, we needed to:
1. Create a PersistentVolume (`mysql-pv`) and a PersistentVolumeClaim (`mysql-pv-claim`) with 250Mi capacity.
2. Create three secrets (`mysql-root-pass`, `mysql-user-pass`, `mysql-db-url`) to store credentials and database configuration.
3. Define specific environment variables mapping to these secrets.
4. Create a Deployment (`mysql-deployment`) using a MySQL image, mounting the storage at `/var/lib/mysql`.
5. Create a NodePort Service (`mysql`) exposing port 30007.

## Key Concepts
- **Kubernetes Secrets:** Securely storing sensitive information like passwords and usernames.
- **PersistentVolume (PV) & PersistentVolumeClaim (PVC):** Managing stateful storage for the database.
- **Kubernetes Deployment:** Managing the MySQL pod.
- **Environment Variables:** Injecting configuration into the container from Secrets.
- **NodePort Service:** Exposing the application on a static port on each node.

## Solution

To access the Kubernetes cluster, we connect to the jump server:
```bash
ssh thor@jump_host.stratos.xfusioncorp.com
```

We created a single manifest file `mysql-deploy.yaml` including all the required resources:

```yaml
# 1. Secrets
apiVersion: v1
kind: Secret
metadata:
  name: mysql-root-pass
type: Opaque
stringData:
  password: YUIidhb667
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-user-pass
type: Opaque
stringData:
  username: kodekloud_tim
  password: YchZHRcLkL
---
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-url
type: Opaque
stringData:
  database: kodekloud_db8
---
# 2. PersistentVolume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
---
# 3. PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
---
# 4. Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-root-pass
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: mysql-db-url
              key: database
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-user-pass
              key: password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# 5. Service
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  type: NodePort
  selector:
    app: mysql
  ports:
    - port: 3306
      targetPort: 3306
      nodePort: 30007
```

We applied the configuration using the following command:

```bash
kubectl apply -f mysql-deploy.yaml
```

## Verification

We verified the deployment and resources using the following commands:

```bash
# Check Deployments, Pods, and Services
kubectl get all

# Check Persistent Volume and Claim
kubectl get pv,pvc

# Check Secrets
kubectl get secrets

# Verify the Service is listening on the correct NodePort
kubectl describe service mysql
```
