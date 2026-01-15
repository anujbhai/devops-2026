
# Kubernetes Hands-on: Using Resource Requests and Limits

In this hands-on session, we will learn how to define **Resource Requests** and **Limits** in Kubernetes.  
We will explore the declarative method (using YAML files) and demonstrate how to configure resource requests and limits for Pods and containers.

---

## 📌 1. Declarative Method (YAML)

We will first create a Pod with resource requests and limits using a YAML file inside **VS Code**.

### Example: `pod-with-resources.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: my-resource-pod          # The unique name of the Pod
spec:                           # The specification of the Pod
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    resources:                  # Resource requests and limits
      requests:                  # Minimum resources the container needs
        cpu: "100m"              # Requesting 100 milli-CPU
        memory: "128Mi"          # Requesting 128 MB of memory
      limits:                    # Maximum resources the container can use
        cpu: "500m"              # Limiting the container to 500 milli-CPU
        memory: "512Mi"          # Limiting the container to 512 MB of memory
    ports:                       # Ports exposed by this container
    - containerPort: 80         # The application listens on port 80
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Pods.
- **kind: Pod** → Defines the object type.
- **metadata** → Includes Pod name (`my-resource-pod`).
- **spec** → Defines desired state.
  - **resources** → Specifies resource requests and limits for the container.
    - **requests** → The minimum amount of resources the container needs to start.
    - **limits** → The maximum amount of resources the container can use.
      - **cpu** → Specifies CPU limits in cores (e.g., `100m` means 100 milli-CPU).
      - **memory** → Specifies memory limits in bytes (e.g., `128Mi` means 128 MiB).

### Commands:

```bash
kubectl apply -f pod-with-resources.yaml    # Create the Pod with resource requests and limits
kubectl get pods                           # Verify Pod status
kubectl describe pod my-resource-pod       # View Pod details
```

---

## 📌 2. Debugging and Troubleshooting Resource Requests and Limits

### View Pod's Resource Requests and Limits
```bash
kubectl get pod my-resource-pod -o yaml | grep resources -A 10
```
- Displays the resource requests and limits of the Pod.

### Describe Pod
```bash
kubectl describe pod my-resource-pod
```
- Shows details of the Pod, including resource requests and limits.

### Access Pod Shell
```bash
kubectl exec -it my-resource-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Pod
```bash
kubectl delete pod my-resource-pod
```

---

## ✅ Summary

- Created a **Pod with resource requests and limits** **declaratively** using YAML.
- Explored how to manage resource requests and limits with `kubectl`.
- Key debugging tools:
  - `kubectl get pods`
  - `kubectl describe pod`
  - `kubectl exec`
  - `kubectl delete`
- Resource requests and limits ensure that Pods receive the necessary resources and do not exceed their allocated limits.

⚡ **Pro Tip**: Setting proper resource requests and limits helps prevent resource contention and ensures that your application runs efficiently.

