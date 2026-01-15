
# Kubernetes Hands-on: Node Affinity

In this hands-on session, we will learn how to use **Node Affinity** in Kubernetes to control on which nodes a Pod can be scheduled. Node affinity allows you to constrain Pods to run only on nodes with certain labels, providing more flexibility than NodeSelector.

---

## 📌 1. What is Node Affinity?

Node Affinity is a set of rules used by the Kubernetes scheduler to determine which nodes a Pod can be placed on. It is defined in the Pod spec under `affinity.nodeAffinity`.

Node Affinity has two types of rules:
1. **requiredDuringSchedulingIgnoredDuringExecution**: Hard requirement. Pods will not be scheduled unless the node matches the affinity rules.
2. **preferredDuringSchedulingIgnoredDuringExecution**: Soft preference. Kubernetes will try to schedule the Pod on a preferred node but will schedule it elsewhere if necessary.

---

## 📌 2. Node Affinity Example (Required)

### Example: `pod-node-affinity-required.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-node-affinity
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

### Key Fields Explained:

- **affinity.nodeAffinity** → Defines node affinity rules for the Pod.
- **requiredDuringSchedulingIgnoredDuringExecution** → Hard requirement for scheduling.
- **nodeSelectorTerms.matchExpressions** → Specifies which node labels must match.
  - **key: disktype** → The node label key.
  - **operator: In** → Operator for the match (could also be `NotIn`, `Exists`, etc.).
  - **values: ["ssd"]** → List of values the node label should have.

### Commands:

```bash
kubectl apply -f pod-node-affinity-required.yaml
kubectl get pods -o wide       # Verify on which node the Pod is scheduled
```

---

## 📌 3. Node Affinity Example (Preferred)

### Example: `pod-node-affinity-preferred.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-node-affinity-preferred
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```

### Key Fields Explained:

- **preferredDuringSchedulingIgnoredDuringExecution** → Soft preference for node scheduling.
- **weight** → Relative importance of this preference (1-100).
- **preference.matchExpressions** → Specifies node label preference.

### Commands:

```bash
kubectl apply -f pod-node-affinity-preferred.yaml
kubectl get pods -o wide       # Verify on which node the Pod is scheduled
```

---

## 📌 4. Debugging and Troubleshooting Node Affinity

### View Node Labels

```bash
kubectl get nodes --show-labels       # List nodes with their labels
```

### Describe Node

```bash
kubectl describe node minikube    # Check labels and conditions on a node
```



### Delete Pod

```bash
kubectl delete pod <pod-name>
```

---

## ✅ Summary

- **Node Affinity** allows Pods to be scheduled on nodes that match specific labels.
- Hard requirements use `requiredDuringSchedulingIgnoredDuringExecution`.
- Soft preferences use `preferredDuringSchedulingIgnoredDuringExecution`.
- Useful for workload placement, performance optimization, and node specialization.
- Key commands:
  - `kubectl apply -f <file>`
  - `kubectl get pods -o wide`
  - `kubectl describe pod <pod-name>`
  - `kubectl get nodes --show-labels`
  - `kubectl delete pod <pod-name>`

⚡ **Pro Tip**: Node Affinity is more flexible than NodeSelector and should be used when you want fine-grained control over Pod placement with both hard and soft rules.
