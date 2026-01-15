
# Kubernetes Hands-on: Using NodeSelector

In this hands-on session, we will learn how to use **NodeSelector** to assign Pods to specific Nodes in Kubernetes.  
We will explore the declarative method (using YAML files) and demonstrate how to use NodeSelector to control where Pods are scheduled.

---

## 📌 1. Declarative Method (YAML)

We will first create a **Pod** with **NodeSelector** using a YAML file inside **VS Code**.

### Example: `pod-with-node-selector.yaml`

```yaml
apiVersion: v1                # The Kubernetes API version for Pods is "v1"
kind: Pod                     # We are creating a resource of type Pod
metadata:                      # Metadata contains identifying information
  name: my-node-selector-pod   # The unique name of the Pod
spec:                         # The specification of the Pod
  nodeSelector:                # Specify NodeSelector for scheduling
    disktype: ssd              # Label key-value pair that Node must match
  containers:                  # List of containers inside this Pod
  - name: nginx-container     # Name of the first container
    image: nginx:latest       # Container image to use (nginx, latest tag)
    ports:                     # Ports exposed by this container
    - containerPort: 80       # The application listens on port 80
```

### Key Fields Explained
- **nodeSelector** → Specifies the label key-value pair that the Pod can be scheduled on.
  - **disktype: ssd** → The Pod will only be scheduled on Nodes with the label `disktype=ssd`.

### Commands:

```bash
kubectl apply -f pod-with-node-selector.yaml    # Create the Pod with NodeSelector
kubectl get pods                       # Verify Pod status

```
---

## 📌 2. Debugging and Troubleshooting NodeSelector

### List Nodes and Their Labels
```bash
kubectl get nodes --show-labels          # List Nodes with labels
```

### Describe Node Labels
```bash
kubectl describe node minikube       # View labels on a specific Node
```

### View Pod Scheduling Details
```bash
kubectl describe pod my-node-selector-pod  # View Pod scheduling details
```



### Delete Pod
```bash
kubectl delete pod my-node-selector-pod
```

---

## ✅ Summary

- Used **NodeSelector** to assign Pods to specific Nodes.
- Explored how to manage NodeSelector with `kubectl`.
- Key debugging tools:
  - `kubectl get nodes`
  - `kubectl describe node`
  - `kubectl describe pod`
  - `kubectl exec`
  - `kubectl delete`
- **NodeSelector** provides a simple way to ensure that Pods are scheduled on specific Nodes based on labels.

⚡ **Pro Tip**: Use **NodeSelector** to schedule Pods based on specific Node labels (e.g., node types, hardware, region, etc.). It's a powerful tool for managing Pod placement in a cluster.

