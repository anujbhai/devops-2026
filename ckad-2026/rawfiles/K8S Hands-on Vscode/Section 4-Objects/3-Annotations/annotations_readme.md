
# Kubernetes Hands-on: Using Annotations 

In this hands-on, we learn how to use Annotations in Kubernetes to attach non-identifying metadata to resources.

## 📌 1. Applying Annotations

Example: pod-with-annotations.yaml
```yaml
# This manifest defines a Pod with metadata annotations 
apiVersion: v1 # API version used for defining Pod object
kind: Pod                 # The resource type is Pod
metadata:
  name: my-annotated-pod         # The name of the Pod
  annotations:      # Annotations are used to attach non-identifying metadata
    description: "Critical NGINX pod"   # A description for this Pod, useful for documentation or automation
    owner: "team-dev"                   # Indicates which team or person owns this Pod
spec:
  containers:                    # The list of containers that run inside the Pod
  - name: nginx                  # Name of the container
    image: nginx:latest          # The container image used (NGINX latest version)
    ports:
    - containerPort: 80          # The port that this container listens on (HTTP)

```

```bash
kubectl apply -f pod-with-annotations.yaml

kubectl describe pod my-annotated-pod
```

## 📌 2. Adding Annotations to Existing Pod

```bash
kubectl annotate pod my-annotated-pod environment="production"
kubectl describe pod my-annotated-pod
```

## 📌 3. Debugging and Cleanup

```bash
kubectl describe pod my-annotated-pod
kubectl delete pod my-annotated-pod
```


# ✅ Summary

Used Annotations to attach metadata to a Pod.

Can be applied at creation or added later.

Useful for operational data, monitoring, or external tools.