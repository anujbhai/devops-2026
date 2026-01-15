
# Kubernetes Hands-on: Using Pod Priority and Preemption

Kubernetes Hands-on: Pod Priority and Preemption (Improved)
Objective:
Demonstrate how Kubernetes schedules Pods based on priority.
Show how high-priority Pods can preempt lower-priority Pods when resources are constrained.

## 📌 1. Create PriorityClasses

priorityclass.yaml
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: low-priority
value: 100
globalDefault: true
description: "Default low priority for normal Pods"

---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
globalDefault: false
description: "High priority for critical workloads"
preemptionPolicy: PreemptLowerPriority
```

kubectl apply -f priorityclass.yaml
kubectl get priorityclass

✅ Explanation:

low-priority is default.
high-priority is for Pods that can preempt lower-priority ones.

## 📌 2. Create a Resource-Constrained Node Environment

For demonstration, we assume a node with limited CPU (e.g., 500m).

Low-priority Pods that consume resources

low-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: low-pod-1
spec:
  priorityClassName: low-priority
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
    resources:
      requests:
        cpu: "250m"
      limits:
        cpu: "250m"
---
apiVersion: v1
kind: Pod
metadata:
  name: low-pod-2
spec:
  priorityClassName: low-priority
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
    resources:
      requests:
        cpu: "250m"
      limits:
        cpu: "250m"
```
kubectl apply -f low-pod.yaml
kubectl get pods -o wide


✅ Explanation:

Both low-priority Pods will consume all available CPU on the node.

Any new Pod requiring CPU cannot be scheduled.

## 📌 3. Deploy a Critical Pod (High-priority, No Preemption)

critical-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: critical-pod
spec:
  priorityClassName: high-priority
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
    resources:
      requests:
        cpu: "100m"
```

kubectl apply -f critical-pod.yaml
kubectl get pods
kubectl describe pod critical-pod


✅ Observation:

The Pod may remain Pending if no resources are available.

It does not preempt lower-priority Pods yet.

## 📌 4.  Deploy a Preemptive Pod (High-priority, Can Evict)

preemptive-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: preemptive-pod
spec:
  priorityClassName: high-priority
  containers:
  - name: busybox
    image: busybox
    command: ["sh", "-c", "sleep 3600"]
    resources:
      requests:
        cpu: "300m"
```
kubectl apply -f preemptive-pod.yaml
kubectl get pods
kubectl describe pod preemptive-pod


✅ Observation:

Kubernetes evicts one or more low-priority Pods to free CPU.
preemptive-pod is now scheduled successfully.

 ## 📌 5. Debugging & Visual Confirmation

kubectl get pods -o wide
kubectl describe pod preemptive-pod
kubectl describe pod low-pod-1
kubectl describe pod low-pod-2


💡 Tip:

Use kubectl get events --sort-by=.metadata.creationTimestamp to see preemption events in real time.

## 📌 6.  Clean Up

kubectl delete pod --all
kubectl delete priorityclass low-priority high-priority


## ✅ Summary

- Created **Pod Priority** and **Preemption** policies using **PriorityClass**. 
- Used **PriorityClass** to assign priority to Pods and allow preemption. 
- Key debugging tools: 
- kubectl get priorityclass 
- kubectl describe priorityclass 
- kubectl describe pod 
- kubectl exec
 -kubectl delete 
 
 - **Pod Priority** helps manage Pod scheduling, and **Preemption** ensures that high-priority Pods are scheduled even if it means evicting lower-priority Pods. 

⚡ **Pro Tip**: Use **Pod Priority and Preemption** to ensure critical workloads are always scheduled and prevent resource starvation for high-priority Pods.
