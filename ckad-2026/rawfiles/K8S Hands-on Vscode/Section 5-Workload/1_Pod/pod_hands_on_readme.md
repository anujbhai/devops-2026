# Kubernetes Hands-on: Pods

In this hands-on session, we will learn how to create, manage, and debug **Pods** in Kubernetes. Pods are the smallest deployable units in Kubernetes and encapsulate one or more containers.

---

## 📌 1. Creating a Pod Declaratively (YAML)

### Example: `pod.yaml`

apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
    ports:
    - containerPort: 80


### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version.
- **kind: Pod** → Defines the object type.
- **metadata** → Pod name and labels.
- **spec** → Pod specification.
  - **containers** → List of containers.
  - **name** → Container name.
  - **image** → Container image.
  - **ports** → Exposed container ports.

### Commands:

kubectl apply -f pod.yaml    # Create the Pod
kubectl get pods             # Verify Pod status


---

## 📌 2. Creating a Pod Imperatively

You can also create a Pod using the `kubectl run` command:

kubectl run my-pod2 --image=nginx:latest --port=80
kubectl get pods


### Explanation
- `kubectl run my-pod` → Creates a Pod named **my-pod**.
- `--image=nginx:latest` → Uses the **nginx:latest** image.
- `--port=80` → Exposes port 80 for HTTP traffic.

---

## 📌 3. Debugging and Troubleshooting Pods

### List Pods

kubectl get pods -n default


### Describe Pod

kubectl describe pod my-pod

- Shows labels, node placement, container details, and events.

### View Logs

kubectl logs my-pod

- Displays container logs to confirm proper startup or reveal errors.

### Access Pod Shell

kubectl exec -it my-pod -- /bin/bash

- Opens an interactive shell inside the container.

### Delete Pod

kubectl delete pod my-pod


---

## ✅ Summary

- Created a Pod **declaratively** using YAML and **imperatively** using `kubectl run`.
- Learned how to debug Pods using `kubectl describe`, `kubectl logs`, and `kubectl exec`.
- Cleaned up resources using `kubectl delete pod`.

⚡ **Pro Tip**: Familiarize yourself with creating, inspecting, and debugging Pods quickly, as this is a fundamental skill for the CKAD exam.
