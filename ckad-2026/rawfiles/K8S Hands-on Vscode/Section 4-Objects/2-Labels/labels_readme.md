# Kubernetes Hands-on: Using Labels 

In this hands-on, we learn how to use Labels in Kubernetes to attach identifying metadata to resources and filter them efficiently.

## 📌 1. Applying Labels to a Pod
Example: pod-with-labels.yaml
```yaml
apiVersion: v1              # API version used for defining the Pod
kind: Pod                   # Resource type is Pod
metadata:                   # Metadata section contains identifying information
  name: my-labeled-pod      # Name of the Pod
  labels:                   # Labels help organize and select objects
    app: nginx              # Label: identifies the application name
    environment: production # Label: identifies the environment (e.g., production)
spec:                       # Specification of the Pod
  containers:               # List of containers running in this Pod
  - name: nginx             # Name of the container
    image: nginx:latest     # Container image to use
    ports:                  # List of ports exposed by the container
    - containerPort: 80     # Exposes port 80 for web traffic (default NGINX port)

```
```bash
kubectl apply -f pod-with-labels.yaml
kubectl get pods --show-labels
kubectl describe pod my-labeled-pod
```

## 📌 2. Adding Labels to Existing Pod
```bash
kubectl label pod my-labeled-pod version=v1
kubectl get pods --show-labels
```

## 📌 3. Selecting Pods Using Labels
```bash
kubectl get pods -l app=nginx
kubectl get pods -l environment=production,version=v1
```

## 📌 4. Debugging and Cleanup
```bash
kubectl describe pod my-labeled-pod
kubectl delete pod my-labeled-pod
```


# ✅ Summary

Used Labels to attach identifying metadata to Pods.

Labels allow filtering and selection using kubectl get -l.

Can be applied at creation or added later.