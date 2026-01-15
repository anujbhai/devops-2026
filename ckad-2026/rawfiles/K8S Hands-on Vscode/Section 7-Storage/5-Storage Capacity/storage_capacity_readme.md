
# Kubernetes Hands-on: Using Storage Capacity

In this hands-on session, we will learn how to configure **Storage Capacity** in Kubernetes.  
We will explore how to set and manage storage capacity for Persistent Volumes (PV) and Persistent Volume Claims (PVC) using YAML files.

---

## 📌 1. Declarative Method (YAML)

We will first create a Persistent Volume (PV) and Persistent Volume Claim (PVC) with specified storage capacity.

### Example: `pv-with-storage-capacity.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Persistent Volumes is "v1"
kind: PersistentVolume           # We are creating a resource of type PersistentVolume
metadata:                        # Metadata contains identifying information
  name: my-storage-pv            # The unique name of the PersistentVolume
spec:                            # The specification of the PersistentVolume
  capacity:                      # Storage capacity of the PersistentVolume
    storage: 5Gi                 # 5Gi of storage
  accessModes:                   # Access modes for the PersistentVolume
    - ReadWriteOnce              # Can be mounted as read-write by a single node
  persistentVolumeReclaimPolicy: Retain  # The policy for when the volume is released
  hostPath:                      # A simple hostPath to simulate persistent storage
    path: /mnt/data/storage       # Path to the volume on the node
```

### Example: `pvc-with-storage-capacity.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Persistent Volume Claims is "v1"
kind: PersistentVolumeClaim      # We are creating a resource of type PersistentVolumeClaim
metadata:                        # Metadata contains identifying information
  name: my-storage-pvc           # The unique name of the PersistentVolumeClaim
spec:                            # The specification of the PersistentVolumeClaim
  accessModes:                   # Access modes for the PersistentVolumeClaim
    - ReadWriteOnce              # Can be mounted as read-write by a single node
  resources:                     # The requested resources for the PersistentVolumeClaim
    requests:
      storage: 5Gi               # Requesting 5Gi of storage
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for PV and PVC.
- **kind: PersistentVolume** → Defines the object type for PV.
- **kind: PersistentVolumeClaim** → Defines the object type for PVC.
- **metadata** → Includes name of the PV and PVC (`my-storage-pv`, `my-storage-pvc`).
- **spec** → Defines desired state for the PV and PVC.
  - **capacity** → Specifies the size of the storage for the PV.
  - **resources.requests.storage** → Specifies the storage requested for the PVC.

### Commands:

```bash
kubectl apply -f pv-with-storage-capacity.yaml     # Create the Persistent Volume
kubectl apply -f pvc-with-storage-capacity.yaml    # Create the Persistent Volume Claim
kubectl get pv                                    # Verify Persistent Volume status
kubectl get pvc                                   # Verify Persistent Volume Claim status
kubectl describe pv my-storage-pv                 # View Persistent Volume details
kubectl describe pvc my-storage-pvc               # View Persistent Volume Claim details
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same Persistent Volume (PV) and Persistent Volume Claim (PVC) with specified storage capacity using the **imperative method** with `kubectl` commands.

### Example:

#### Create PV:

```bash
kubectl create pv my-storage-pv --capacity=5Gi --access-mode=ReadWriteOnce --path=/mnt/data/storage
kubectl get pv
```

#### Create PVC:

```bash
kubectl create pvc my-storage-pvc --size=5Gi --access-mode=ReadWriteOnce
kubectl get pvc
```

Explanation:  
- `kubectl create pv` → Creates a Persistent Volume with 5Gi capacity.
- `kubectl create pvc` → Creates a Persistent Volume Claim with 5Gi requested storage.

---

## 📌 3. Debugging and Troubleshooting Storage Capacity

### View PV and PVC Details
```bash
kubectl describe pv my-storage-pv               # View Persistent Volume details
kubectl describe pvc my-storage-pvc             # View Persistent Volume Claim details
```

### Check Pod's Volume Mount
```bash
kubectl describe pod my-pod                     # View volume mounts in the Pod
```

### Access Pod Shell
```bash
kubectl exec -it my-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete PV and PVC
```bash
kubectl delete pv my-storage-pv
kubectl delete pvc my-storage-pvc
```

---

## ✅ Summary

- Created **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** with storage capacity **declaratively** using YAML.
- Explored how to manage storage capacity with `kubectl`.
- Key debugging tools:
  - `kubectl get pv`
  - `kubectl get pvc`
  - `kubectl describe pv`
  - `kubectl describe pvc`
  - `kubectl exec`
  - `kubectl delete`
- PVs and PVCs allow you to manage and allocate storage resources in Kubernetes.

⚡ **Pro Tip**: Always ensure that your Persistent Volume and Persistent Volume Claim have matching storage capacities to avoid scheduling issues.

