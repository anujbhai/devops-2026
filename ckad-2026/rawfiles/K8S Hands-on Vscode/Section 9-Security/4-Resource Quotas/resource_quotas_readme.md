
# Kubernetes Hands-on: Using Resource Quotas

In this hands-on session, we will learn how to configure **Resource Quotas** in Kubernetes to manage resource usage in namespaces.  
We will explore how to define and apply resource quotas for CPU, memory, and other resources in a namespace.

---

## 📌 1. Declarative Method (YAML)

We will first create a **ResourceQuota** using a YAML file inside **VS Code**.

### Example: `resource-quota.yaml`

```yaml
apiVersion: v1                  # API version for ResourceQuota
kind: ResourceQuota             # Defines the object type
metadata:                       # Metadata section
  name: my-resource-quota       # Unique name for the ResourceQuota
  namespace: defautl            # (Optional) Namespace where it applies
spec:                           # Specification of the quota
  hard:                         # Hard limits for resource usage
    pods: "10"                  # Maximum number of Pods in this namespace
    requests.cpu: "2"           # Total sum of CPU requests allowed
    requests.memory: "4Gi"      # Total sum of memory requests allowed
    limits.cpu: "4"             # Total sum of CPU limits allowed
    limits.memory: "8Gi"        # Total sum of memory limits allowed
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for ResourceQuotas.
- **kind: ResourceQuota** → Defines the object type for ResourceQuota.
- **metadata** → Includes ResourceQuota name (`my-resource-quota`).
- **spec.hard** → Defines hard limits on the resources (CPU, memory, etc.).
  - **cpu** → The total amount of CPU resources that can be requested by all Pods in the namespace.
  - **memory** → The total amount of memory resources that can be requested by all Pods in the namespace.
  - **pods** → The maximum number of Pods that can be created in the namespace.
  - **requests.cpu** and **requests.memory** → The minimum resources required for the namespace.
  - **limits.cpu** and **limits.memory** → The maximum resources that can be allocated for the namespace.

### Commands:

```bash
kubectl apply -f resource-quota.yaml    # Create the ResourceQuota
kubectl get resourcequota              # Verify ResourceQuota status

```

---


## 📌 2. Debugging and Troubleshooting Resource Quotas

### View ResourceQuota Details
```bash
kubectl describe resourcequota my-resource-quota   # View ResourceQuota details
```

### Check Resource Usage in Namespace
```bash
kubectl describe quota -n default               # View resource usage in the default namespace
```

##  📌 3. Testing ResourceQuota with Pods

Now that we have defined our ResourceQuota, we will test it by creating two Pods:

A Pod that respects the quota and runs successfully.

A Pod that exceeds the quota and fails to create.

✅ Example 1: Pod Within Quota — pod-ok.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-ok
  namespace: default
spec:
  containers:
    - name: nginx
      image: nginx:latest
      resources:
        requests:
          cpu: "500m"          # 0.5 CPU
          memory: "512Mi"      # 0.5 GiB memory
        limits:
          cpu: "1"             # 1 CPU max
          memory: "1Gi"        # 1 GiB max
```

Run the Pod:
```bash
kubectl apply -f pod-ok.yaml
kubectl get pods
```

This Pod will run successfully because its requests and limits are within the defined quota.

❌ Example 2: Pod Exceeding Quota — pod-exceed.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-exceed
  namespace: default
spec:
  containers:
    - name: nginx
      image: nginx:latest
      resources:
        requests:
          cpu: "3"             # ❌ exceeds requests.cpu limit (2)
          memory: "6Gi"        # ❌ exceeds requests.memory limit (4Gi)
        limits:
          cpu: "6"             # ❌ exceeds limits.cpu (4)
          memory: "10Gi"       # ❌ exceeds limits.memory (8Gi)
```

Try to create the Pod:

```bash
kubectl apply -f pod-exceed.yaml
```

Expected output:

Error from server (Forbidden): exceeded quota: my-resource-quota,
requested: requests.cpu=3, requests.memory=6Gi, used: requests.cpu=0, requests.memory=0,
limited: requests.cpu=2, requests.memory=4Gi


### Enable Metrics Server in Minikube using the command :

```bash
minikube addons enable metrics-server
```
```bash
kubectl top pod --namespace=default         # Check resource usage of Pods
```

### Check Resource Usage of a Specific Pod
```bash
kubectl top pod pod-ok # Check resource usage of the Pod
```


### Delete ResourceQuota and Pod
```bash
kubectl delete resourcequota my-resource-quota
kubectl delete pod pod-ok
```

---

## ✅ Summary

- Created a **ResourceQuota** **declaratively** using YAML.
- Explored how to manage ResourceQuotas with `kubectl`.
- Key debugging tools:
  - `kubectl get resourcequota`
  - `kubectl describe resourcequota`
  - `kubectl describe quota`
  - `kubectl get pods`
  - `kubectl top pod`
  - `kubectl exec`
  - `kubectl delete`
- ResourceQuotas help manage resource usage in a namespace by setting limits on CPU, memory, and the number of Pods.

⚡ **Pro Tip**: ResourceQuotas help ensure fair resource distribution in a multi-tenant Kubernetes environment. Use them to control the resource consumption in namespaces.

