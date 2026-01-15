# Kubernetes Hands-on: Deployment

In this hands-on session, we will create a **Deployment**, which allows us to manage multiple replicas of a Pod and perform rolling updates.  
We will use both declarative and imperative methods, and finally explore debugging and troubleshooting.

---



## 📌 1. Declarative Method (YAML)

### Example: `deployment.yaml`

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        ports:
        - containerPort: 80

### Explanation
- apiVersion: apps/v1 → API version for Deployment.
- kind: Deployment → Resource type.
- metadata → Deployment name (my-deployment) and labels (app: nginx).
- spec.replicas: 3 → Ensures three Pods are always running.
- selector → Identifies Pods managed by this Deployment.
- template → Pod template, including container spec.

### Commands:

kubectl apply -f deployment.yaml

kubectl get deployments -n default


---


## 📌 2. Imperative Method


kubectl create deployment my-deployment --image=nginx:latest --replicas=3

### Explanation
Creates a Deployment named my-deployment.
Uses nginx:latest as the image.
Maintains 3 replicas.

## 📌 3. Debugging and Troubleshooting Pods


kubectl get deployments -n default
kubectl describe deployment my-deployment
kubectl get pods -l app=my-deployment -n default
kubectl logs <pod-name>
kubectl rollout status deployment/my-deployment
kubectl delete deployment my-deployment

- get deployments → Lists Deployments with replica count and status.
- describe → Shows Pod template, events, rollout history.
- get pods -l app=nginx → Lists Pods managed by the Deployment.
- logs → View logs of a specific Pod.
- ollout status → Check rollout progress.
- delete deployment → Removes Deployment and its Pods.

✅ Summary
 Created a Deployment with YAML.

Created the same Deployment using kubectl create deployment.

Learned debugging commands for Pods and rollout status.

⚡ Deployments are fundamental for managing multiple replicas and ensuring application availability with rolling updates.


