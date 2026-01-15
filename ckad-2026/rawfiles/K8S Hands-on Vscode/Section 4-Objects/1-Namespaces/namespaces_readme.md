# Kubernetes Hands-on: Using Namespaces 

In this hands-on, we learn how to use Namespaces to organize and isolate Kubernetes resources.

## 📌 1. Creating a Namespace
```yaml
apiVersion: v1        # API version for core Kubernetes objects like Namespace
kind: Namespace       # Resource type is Namespace
metadata:             # Metadata contains identifying information
  name: dev           # Name of the namespace to be created (here: "dev")

```
```bash
kubectl apply -f namespace.yaml
kubectl get namespaces
```

## 📌 2. Using a Namespace with Resources
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: dev
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

```bash
kubectl apply -f pod.yaml
kubectl get pods -n dev
```


## 📌 3. Switching Context to a Namespace

```bash
kubectl config set-context --current --namespace=dev
kubectl get pods
```

## 📌 4. Deleting a Namespace

```bash
kubectl delete namespace dev
```



# ✅ Summary

Used Namespaces to isolate and organize resources.

Created resources inside a specific namespace.

Switched context to a namespace for easier management.