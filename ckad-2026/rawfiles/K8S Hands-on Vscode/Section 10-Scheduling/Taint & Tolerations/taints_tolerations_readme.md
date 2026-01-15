
# Kubernetes Hands-on: Using Taints and Tolerations

In this hands-on session, we will learn how to use **Taints** and **Tolerations** in Kubernetes to control which Pods can be scheduled on specific Nodes.  
We will explore the declarative method (using YAML files) and demonstrate how to configure and use Taints and Tolerations.

---

## 📌 1. Taints

**Taints** allow you to mark a Node so that no Pods can be scheduled on it unless they have a matching **Toleration**.

### Example: `taint-node.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Nodes is "v1"
kind: Node                       # We are creating a resource of type Node
metadata:                        # Metadata contains identifying information
  name: node-1                   # The name of the Node
spec:                           # The specification of the Node
  taints:                        # Define taints on the Node
  - effect: NoSchedule           # Pods cannot be scheduled on this Node
    key: disktype                # The taint key
    value: ssd                   # The taint value
```

### Key Fields Explained
- **taints** → Defines the taints applied to the Node.
  - **effect: NoSchedule** → No Pods can be scheduled on this Node unless they have a matching Toleration.
  - **key** → Specifies the taint key (e.g., `disktype`).
  - **value** → Specifies the taint value (e.g., `ssd`).

### Commands:

```bash
kubectl apply -f taint-node.yaml    # Apply the taint to the Node
kubectl describe node node-1        # Verify the taint applied to the Node
```

---

## 📌 2. Tolerations

**Tolerations** allow Pods to be scheduled on Nodes that have matching taints. This is the mechanism that lets Pods "tolerate" taints on Nodes.

### Example: `pod-with-tolerations.yaml`

```yaml
apiVersion: v1                # The Kubernetes API version for Pods is "v1"
kind: Pod                     # We are creating a resource of type Pod
metadata:                      # Metadata contains identifying information
  name: my-tolerated-pod       # The unique name of the Pod
spec:                         # The specification of the Pod
  tolerations:                 # Define Tolerations for scheduling
  - key: disktype              # The taint key that this Pod will tolerate
    operator: "Equal"          # The operator to match the taint value
    value: ssd                 # The taint value that this Pod will tolerate
    effect: NoSchedule         # Tolerate the NoSchedule effect
  containers:                  # List of containers inside this Pod
  - name: nginx-container     # Name of the first container
    image: nginx:latest       # Container image to use (nginx, latest tag)
    ports:                     # Ports exposed by this container
    - containerPort: 80       # The application listens on port 80
```

### Key Fields Explained
- **tolerations** → Specifies the Tolerations that allow the Pod to be scheduled on tainted Nodes.
  - **key** → The taint key that the Pod will tolerate (e.g., `disktype`).
  - **value** → The taint value that the Pod will tolerate (e.g., `ssd`).
  - **effect: NoSchedule** → The Pod will tolerate the **NoSchedule** effect.

### Commands:

```bash
kubectl apply -f pod-with-tolerations.yaml    # Create the Pod with Tolerations
kubectl get pods                              # Verify Pod status
kubectl describe pod my-tolerated-pod        # View Pod details
```

---

## 📌 3. Debugging and Troubleshooting Taints and Tolerations

### View Node Taints
```bash
kubectl describe node node-1                  # View taints on a specific Node
```

### View Pod's Tolerations
```bash
kubectl describe pod my-tolerated-pod        # View Tolerations of the Pod
```

### Check Pod Scheduling
```bash
kubectl get pods --field-selector=status.phase=Pending  # Check if the Pod is pending due to scheduling issues
```

### Access Pod Shell
```bash
kubectl exec -it my-tolerated-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Taints and Pods
```bash
kubectl taint nodes node-1 disktype=ssd:NoSchedule   # Remove taint from Node
kubectl delete pod my-tolerated-pod                  # Delete Pod
```

---

## ✅ Summary

- Used **Taints** to mark Nodes with restrictions for scheduling Pods.
- Used **Tolerations** in Pods to allow them to be scheduled on tainted Nodes.
- Key debugging tools:
  - `kubectl describe node`
  - `kubectl describe pod`
  - `kubectl get pods`
  - `kubectl exec`
  - `kubectl taint`
  - `kubectl delete`
- Taints and Tolerations help control the placement of Pods on Nodes based on security, performance, or resource requirements.

⚡ **Pro Tip**: Use Taints and Tolerations to enforce advanced scheduling rules in your Kubernetes cluster, such as separating workloads with different requirements or isolating sensitive workloads.

