
# Kubernetes Hands-on: Creating a ReplicaSet

In this hands-on session, we will create a **ReplicaSet** in Kubernete which ensures that a specified number of Pod replicas are running at all times. 

We will explore the declarative method (using a YAML file), and learn how to manage ReplicaSets using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We first create a ReplicaSet using a YAML file inside **VS Code**.

### Example: `replicaset.yaml`


apiVersion: apps/v1                # The Kubernetes API version for ReplicaSets is "apps/v1"
kind: ReplicaSet                   # We are creating a resource of type ReplicaSet
metadata:                          # Metadata contains identifying information
  name: nginx-replicaset              # The unique name of the ReplicaSet
  labels:                          # Labels help organize and select the ReplicaSet
    app: nginx-replica             # Here we label it "app=nginx-replica"
spec:                               # The specification of the ReplicaSet
  replicas: 3                       # Number of Pod replicas to maintain
  selector:                         # Selector defines the Pods the ReplicaSet manages
    matchLabels:
      app: nginx-replica           # Match the Pods with this label "app=nginx-replica"
  template:                         # Template for the Pods in the ReplicaSet
    metadata:                       # Metadata for the Pods
      labels:
        app: nginx-replica         # Match label for Pods
    spec:                           # Specification of the Pods
      containers:                   # List of containers inside each Pod
      - name: nginx-container       # Name of the first container
        image: nginx:latest         # Container image to use (nginx, latest tag)
        ports:                      # Ports exposed by this container
        - containerPort: 80         # The application listens on port 80


### Key Fields Explained

- **apiVersion: apps/v1** → Kubernetes API version for ReplicaSets.
- **kind: ReplicaSet** → Defines the object type.
- **metadata** → Includes ReplicaSet name(`nginx-replicaset`) and labels (e.g., `app: nginx-replica`).
- **spec** → Defines desired state.
  - **replicas** → The number of Pods (3 in this case).
  - **selector** → Ensures that the ReplicaSet manages Pods with matching labels.
  - **template** → Pod template for the ReplicaSet.
    - **containers** → Specifies the container configuration (e.g., `nginx-container`, image `nginx:latest`, port `80`).

 
###   Commands:


kubectl apply -f replicaset.yaml    # Create the ReplicaSet



## 📌 2. Debugging and Troubleshooting ReplicaSets

### List ReplicaSets

kubectl get rs -n default


### Describe ReplicaSet

kubectl describe rs nginx-replicaset


- Shows labels, pod template details, and events (scheduling, image pulls).
- Look at the **Events** section for troubleshooting.

### view pods :

kubectl get pods -l app=nginx-replica  -n default                  # Verify Pods are running

### View Pods Logs

kubectl logs nginx-replicaset <pod-id>

- Displays logs for a specific Pod in the ReplicaSet.
- Useful for troubleshooting individual Pod issues.

### Access Pod Shell

kubectl exec -it nginx-replicaset <pod-id> -- /bin/bash

- Opens an interactive shell inside the specified Pod.


### 📌 3. Delete Pod to see the role of replicasets :

kubectl delete pod nginx-replicaset <pod-id>

- Deleting Pods will trigger the ReplicaSet to recreate them based on the desired state.


### Delete ReplicaSet for cleanup

kubectl delete replicaset nginx-replicaset 



## ✅ Summary

- Created a ReplicaSet **declaratively** using YAML.
- Explored how to manage ReplicaSets with `kubectl`.
- Key debugging tools:
  - `kubectl get replicasets`
  - `kubectl describe replicaset`
  - `kubectl logs`
  - `kubectl exec`
  - `kubectl delete`
- ReplicaSets ensure that a specific number of Pod replicas are always running.

⚡ **Pro Tip**: ReplicaSets are a key component in scaling your applications. Be sure to understand how they maintain the desired number of replicas.

