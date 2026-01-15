
# Kubernetes Hands-on: Using Probes (Liveness, Readiness, and Startup)

In this hands-on session, we will learn how to configure **Probes** (Liveness, Readiness, and Startup) in Kubernetes to monitor and manage the health of Pods and containers.  
We will explore the declarative method (using YAML files) and demonstrate how to configure and manage probes.

---

## 📌 1. Declarative Method (YAML)

We will first create a Pod with probes using a YAML file inside **VS Code**.

### Example: `pod-with-probes.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: my-probe-pod             # The unique name of the Pod
spec:                           # The specification of the Pod
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    ports:                       # Ports exposed by this container
    - containerPort: 80         # The application listens on port 80
    livenessProbe:               # Liveness probe configuration
      httpGet:
        path: /          # Path to check the container health
        port: 80                 # Port to check
      initialDelaySeconds: 3     # Delay before the probe is started
      periodSeconds: 5          # Frequency of probe execution
    readinessProbe:              # Readiness probe configuration
      httpGet:
        path: /        # Path to check if the container is ready
        port: 80                 # Port to check
      initialDelaySeconds: 3     # Delay before the probe is started
      periodSeconds: 5          # Frequency of probe execution
    startupProbe:                # Startup probe configuration
      httpGet:
        path: /          # Path to check if the container has started
        port: 80                 # Port to check
      initialDelaySeconds: 3     # Delay before the probe is started
      periodSeconds: 5          # Frequency of probe execution
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Pods.
- **kind: Pod** → Defines the object type.
- **metadata** → Includes Pod name (`my-probe-pod`).
- **spec** → Defines desired state.
  - **livenessProbe** → Defines the Liveness probe to check if the container is still running.
  - **readinessProbe** → Defines the Readiness probe to check if the container is ready to accept traffic.
  - **startupProbe** → Defines the Startup probe to check if the container has successfully started.

### Commands:

```bash
kubectl apply -f pod-with-probes.yaml    # Create the Pod with probes
kubectl get pods                        # Verify Pod status
kubectl describe pod my-probe-pod       # View Pod details
```

---


## 📌 2. Debugging and Troubleshooting Probes

### View Pod's Probe Configuration
```bash
kubectl describe pod my-probe-pod | grep -A 10 'Liveness'   # View Liveness probe details
kubectl describe pod my-probe-pod | grep -A 10 'Readiness'  # View Readiness probe details
kubectl describe pod my-probe-pod | grep -A 10 'Startup'    # View Startup probe details
```

### Check Pod Logs for Probe Failures
```bash
kubectl logs my-probe-pod
```
- Displays the logs for the Pod and container.
- Useful for troubleshooting if the probes fail.

### Access Pod Shell
```bash
kubectl exec -it my-probe-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Pod
```bash
kubectl delete pod my-probe-pod
```

---

## ✅ Summary

- Created a **Pod with probes** **declaratively** using YAML.
- Explored how to manage probes (Liveness, Readiness, and Startup) with `kubectl`.
- Key debugging tools:
  - `kubectl get pods`
  - `kubectl describe pod`
  - `kubectl logs`
  - `kubectl exec`
  - `kubectl delete`
- Probes are essential for ensuring the health and readiness of Pods in Kubernetes.

⚡ **Pro Tip**: Properly configure probes to ensure that Kubernetes can automatically handle Pod failures and restarts, improving application reliability.

