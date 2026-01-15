
# Kubernetes Hands-on: Creating a StatefulSet

In this hands-on session, we will create a **StatefulSet** in Kubernetes.  
We will explore the declarative method (using a YAML file) and learn how to manage StatefulSets using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We first create a StatefulSet using a YAML file inside **VS Code**.

### Example: `statefulset.yaml`

```yaml
apiVersion: apps/v1                # The Kubernetes API version for StatefulSets is "apps/v1"
kind: StatefulSet                  # We are creating a resource of type StatefulSet
metadata:                          # Metadata contains identifying information
  name: nginx-stateful            # The unique name of the StatefulSet
  labels:                          # Labels help organize and select the StatefulSet
    app: nginx           # Here we label it "app=nginx-stateful"
spec:                               # The specification of the StatefulSet
  serviceName: "nginx-service"              # The name of the service associated with this StatefulSet
  replicas: 3                       # Number of pod replicas we want to run
  selector:                         # Selector defines the Pods the StatefulSet manages
    matchLabels:
      app: nginx          # Match the Pods with this label "app=nginx-stateful"
  template:                         # Template for the Pods in the StatefulSet
    metadata:                       # Metadata for the Pods
      labels:
        app: nginx        # Match label for Pods
    spec:                           # Specification of the Pods
      containers:                   # List of containers inside each Pod
      - name: nginx-container       # Name of the first container
        image: nginx:latest         # Container image to use (nginx, latest tag)
        ports:                      # Ports exposed by this container
        - containerPort: 80         # The application listens on port 80
  volumeClaimTemplates:             # Persistent storage for StatefulSet Pods
  - metadata:
      name: nginx-persistent-storage # Name for the PersistentVolumeClaim
    spec:
      accessModes: [ "ReadWriteOnce" ] # Only one pod can write to the volume at a time
      resources:
        requests:
          storage: 1Gi             # Requesting 1Gi of storage for each Pod
```

### Key Fields Explained
- **apiVersion: apps/v1** → Kubernetes API version for StatefulSets.
- **kind: StatefulSet** → Defines the object type.
- **metadata** → Includes StatefulSet name (`nginx-stateful`) and labels (e.g., `app: nginx`).
- **spec** → Defines desired state.
  - **serviceName** → The name of the headless service for the StatefulSet.
  - **replicas** → The number of Pods (3 in this case).
  - **selector** → Ensures that the StatefulSet manages Pods with matching labels.
  - **template** → Pod template for the StatefulSet.
    - **containers** → Specifies the container configuration (e.g., `nginx-container`, image `nginx:latest`, port `80`).
  - **volumeClaimTemplates** → Defines persistent storage for each Pod in the StatefulSet.

### Commands:

```bash
kubectl apply -f statefulset.yaml    # Create the StatefulSet

```
---

## 📌 2. Debugging and Troubleshooting StatefulSets

### List StatefulSets
```bash

kubectl get statefulsets -n default # Verify StatefulSet status
      

kubectl describe statefulset nginx-stateful

- Shows labels, pod template details, and events (scheduling, image pulls).
- Look at the **Events** section for troubleshooting.

kubectl get pods  -l app=nginx  -n default                 # Verify Pods are running
```

### View Pods Logs
```bash
kubectl logs nginx-stateful-0
```
- Displays logs for the first Pod (`nginx-stateful-0`).
- Useful for troubleshooting individual Pod issues.

### Access Pod Shell
```bash
kubectl exec -it nginx-stateful-0 -- /bin/bash
```
- Opens an interactive shell inside the first Pod of the StatefulSet.

### 📌 3.  Delete Pods to see the role of statefulsets
```bash
kubectl delete pod nginx-stateful-0
kubectl delete pod nginx-stateful-1
kubectl delete pod nginx-stateful-2

let's see if the pod is restarted : 

 --> kubectl get pods 


In a StatefulSet, deleting a Pod does not permanently remove it.

The StatefulSet controller detects the missing Pod and automatically creates a new one with the same name and ordinal index (e.g., nginx-stateful-2).


### Delete StatefulSet
```bash
kubectl delete statefulset nginx-stateful
```



This ensures the desired state is maintained.



## ✅ Summary

- Created a StatefulSet **declaratively** using YAML.
- Explored how to manage StatefulSets with `kubectl`.
- Key debugging tools:
  - `kubectl get statefulsets`
  - `kubectl describe statefulset`
  - `kubectl logs`
  - `kubectl exec`
  - `kubectl delete`
- StatefulSets provide unique identities for Pods and persistent storage.

⚡ **Pro Tip**: Practice these commands regularly to manage stateful workloads with confidence during your CKAD exam!
