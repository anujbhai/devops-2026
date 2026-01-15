
# Kubernetes Hands-on: Using LimitRange

In this hands-on session, we will learn how to configure **LimitRange** in Kubernetes to manage the resource limits for Pods and containers within a namespace.  
We will explore how to define resource limits and defaults using LimitRange.

---

## 📌 1. Declarative Method (YAML)

We will first create a **LimitRange** using a YAML file inside **VS Code**.

### Example: `limit-range.yaml`

```yaml
apiVersion: v1                # The Kubernetes API version for LimitRange is "v1"
kind: LimitRange              # We are creating a resource of type LimitRange
metadata:                      # Metadata contains identifying information
  name: my-limit-range        # The unique name of the LimitRange
  namespace: default          # The namespace where the LimitRange will apply
spec:                         # The specification of the LimitRange
  limits:                      # Define resource limits
  - type: Container            # This limit applies to containers
    max:
      cpu: "1"                 # Maximum CPU usage (1 core)
      memory: "2Gi"            # Maximum memory usage (2 GiB)
    min:
      cpu: "100m"              # Minimum CPU usage (100 milli-CPU)
      memory: "128Mi"          # Minimum memory usage (128 MiB)
    default:
      cpu: "500m"              # Default CPU usage (500 milli-CPU) if not specified
      memory: "512Mi"          # Default memory usage (512 MiB) if not specified
    defaultRequest:
      cpu: "200m"              # Default requested CPU (200 milli-CPU) if not specified
      memory: "256Mi"          # Default requested memory (256 MiB) if not specified
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for LimitRange.
- **kind: LimitRange** → Defines the object type.
- **metadata** → Includes LimitRange name (`my-limit-range`) and namespace (`default`).
- **spec.limits** → Defines the limits for containers.
  - **max** → Maximum values for CPU and memory.
  - **min** → Minimum values for CPU and memory.
  - **default** → Default values for CPU and memory if not set by the container.
  - **defaultRequest** → Default requested values for CPU and memory if not set.

### Commands:

```bash
kubectl apply -f limit-range.yaml    # Create the LimitRange
kubectl get limitrange               # Verify LimitRange status
```

---


## 📌 2. Debugging and Troubleshooting LimitRange

### View LimitRange Details
```bash
kubectl describe limitrange my-limit-range   # View LimitRange details

```

## 📌 3. Pod Example that Respects the LimitRange

Now let’s create a Pod that respects the configured limits.

Example: pod-within-limitrange.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-within-limits
  namespace: default
spec:
  containers:
  - name: demo-container
    image: nginx:alpine
    resources:
      requests:
        cpu: "300m"       # Within the allowed range (≥100m and ≤1)
        memory: "256Mi"   # Within the allowed range (≥128Mi and ≤2Gi)
      limits:
        cpu: "800m"       # Below max limit (1 core)
        memory: "1Gi"     # Below max limit (2Gi)
```

Commands:

```bash
kubectl apply -f pod-within-limitrange.yaml
kubectl get pods
kubectl describe pod pod-within-limits
```

✅ The Pod should be created successfully, showing resource requests and limits within the defined range.

### Check Resource Usage in the Pod
```bash
kubectl top pod my-pod -n default         # Check resource usage for the Pod
```

## 📌 4. Pod example that violates the LimitRange by requesting more CPU and memory than allowed:
```yaml
Example: pod-violates-limitrange.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-exceeding-limits
  namespace: default
spec:
  containers:
  - name: demo-container
    image: nginx:alpine
    resources:
      requests:
        cpu: "2"        # Exceeds the LimitRange max CPU of 1
        memory: "3Gi"   # Exceeds the LimitRange max memory of 2Gi
      limits:
        cpu: "2"        # Exceeds the LimitRange max CPU
        memory: "3Gi"   # Exceeds the LimitRange max memory
```
Commands:

```bash
kubectl apply -f pod-exceeding-limits.yaml
```

You should see an error like:

Error from server (Forbidden): Pod "pod-exceeding-limits" is forbidden: exceeded quota: my-limit-range, requested: limits.cpu=2, limits.memory=3Gi, used: limits.cpu=0, limits.memory=0, limited: limits.cpu=1, limits.memory=2Gi



### Delete LimitRange and Pod
```bash
kubectl delete limitrange my-limit-range
kubectl delete pod pod-exceeding-limits pod-within-limits
```

---

## ✅ Summary

- Created a **LimitRange** **declaratively** using YAML.
- Explored how to manage resource limits with `kubectl`.
- Key debugging tools:
  - `kubectl get limitrange`
  - `kubectl describe limitrange`
  - `kubectl describe pod`
  - `kubectl top pod`
  - `kubectl exec`
  - `kubectl delete`
- LimitRanges help manage resource usage within a namespace by setting minimum, maximum, and default resource values for containers.

⚡ **Pro Tip**: LimitRanges are useful in multi-tenant clusters to ensure fair resource distribution and prevent a single Pod from consuming excessive resources.

