# Kubernetes Hands-on: Multi-Container Pod Design Patterns

In this hands-on, we’ll explore two important Pod design patterns:
Init Containers and Sidecar Containers.

## 📌 1. Create a Pod with an Init Container

The init container runs before the main application container.
It’s typically used to perform setup tasks (like waiting for a service, initializing data, etc.).

### init-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  initContainers:
  - name: init-wait
    image: busybox
    command: ['sh', '-c', 'echo "Initializing..."; sleep 5']
  containers:
  - name: app
    image: nginx
    ports:
    - containerPort: 80
```
kubectl apply -f init-pod.yaml
kubectl get pods


💡 You’ll see the Pod in Init status until the init container finishes,
then it transitions to Running.

## 📌 2. Create a Pod with a Sidecar Container

The sidecar pattern runs an additional container alongside the main one,
providing a supporting service (like logging, syncing, or proxying).

### sidecar-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-demo
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
  - name: log-agent
    image: busybox
    command: ['sh', '-c', 'while true; do echo "Collecting logs..."; sleep 5; done']
```

kubectl apply -f sidecar-pod.yaml
kubectl logs sidecar-demo -c log-agent


💡 Both containers run in the same Pod, sharing the same network and storage context.

# ✅ Summary

Init containers → run before app containers to prepare the environment.

Sidecars → run alongside the main container to provide continuous support.

These patterns are key for CKAD and real-world Kubernetes design.