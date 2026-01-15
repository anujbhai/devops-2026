
# Kubernetes Hands-on: Dynamic Volume Provisioning

In this hands-on session, we will learn how to configure **Dynamic Volume Provisioning** in Kubernetes.  
We will explore how to set up a **StorageClass** and **Persistent Volume Claim (PVC)** for dynamic provisioning of storage.

---

## 📌 1. Declarative Method (YAML)

We will first create a **StorageClass** and **PersistentVolumeClaim (PVC)** for dynamic volume provisioning.

### Example: `storage-class-dynamic.yaml`

```yaml
apiVersion: storage.k8s.io/v1           # The Kubernetes API version for StorageClasses is "storage.k8s.io/v1"
kind: StorageClass                     # We are creating a resource of type StorageClass
metadata:                              # Metadata contains identifying information
  name: my-dynamic-storage-class       # The unique name of the StorageClass
provisioner: kubernetes.io/aws-ebs     # The provisioner to create EBS volumes dynamically
parameters:                            # Parameters for the provisioner
  type: gp2                             # EBS volume type (gp2 for general-purpose SSD)
reclaimPolicy: Retain                  # Retain the volume when the PVC is deleted
volumeBindingMode: WaitForFirstConsumer # Wait until a Pod is scheduled before provisioning
```

### Example: `pvc-dynamic.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Persistent Volume Claims is "v1"
kind: PersistentVolumeClaim      # We are creating a resource of type PersistentVolumeClaim
metadata:                        # Metadata contains identifying information
  name: my-dynamic-pvc           # The unique name of the PersistentVolumeClaim
spec:                            # The specification of the PersistentVolumeClaim
  accessModes:                   # Access modes for the PersistentVolumeClaim
    - ReadWriteOnce              # Can be mounted as read-write by a single node
  resources:                     # The requested resources for the PersistentVolumeClaim
    requests:
      storage: 5Gi               # Requesting 5Gi of storage
  storageClassName: my-dynamic-storage-class  # Reference to the dynamic StorageClass
```

### Key Fields Explained
- **apiVersion: storage.k8s.io/v1** → Kubernetes API version for StorageClasses.
- **kind: StorageClass** → Defines the object type for StorageClass.
- **provisioner** → Specifies the provisioner to create dynamic volumes (e.g., `kubernetes.io/aws-ebs` for AWS EBS).
- **parameters** → Specifies parameters for the provisioner (e.g., volume type).
- **reclaimPolicy** → Defines what happens to the volume after the PVC is deleted (`Retain` keeps the volume).
- **volumeBindingMode** → Specifies when the volume is provisioned, typically `WaitForFirstConsumer` for delayed provisioning.
- **storageClassName** → The name of the StorageClass used to provision the volume.

### Commands:

```bash
kubectl apply -f storage-class-dynamic.yaml     # Create the StorageClass
kubectl apply -f pvc-dynamic.yaml               # Create the Persistent Volume Claim
kubectl get pvc                                # Verify PVC status
kubectl describe pvc my-dynamic-pvc            # View PVC details
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same **StorageClass** and **PersistentVolumeClaim (PVC)** for dynamic provisioning using the **imperative method** with `kubectl` commands.

### Example:

#### Create StorageClass:

```bash
kubectl create storageclass my-dynamic-storage-class --provisioner=kubernetes.io/aws-ebs --parameters=type=gp2 --reclaim-policy=Retain --volume-binding-mode=WaitForFirstConsumer
kubectl get storageclass
```

#### Create PVC:

```bash
kubectl create pvc my-dynamic-pvc --size=5Gi --access-mode=ReadWriteOnce --storage-class=my-dynamic-storage-class
kubectl get pvc
```

Explanation:  
- `kubectl create storageclass my-dynamic-storage-class` → Creates a dynamic StorageClass with AWS EBS provisioner.
- `kubectl create pvc my-dynamic-pvc` → Creates a PVC that requests storage using the dynamic StorageClass.

---

## 📌 3. Debugging and Troubleshooting Dynamic Volume Provisioning

### View PVC and StorageClass Details
```bash
kubectl describe pvc my-dynamic-pvc               # View Persistent Volume Claim details
kubectl describe storageclass my-dynamic-storage-class  # View StorageClass details
```

### View Provisioned Volume
```bash
kubectl get pv                                    # Verify Persistent Volumes created dynamically
kubectl describe pv my-dynamic-pv                # View Persistent Volume details
```

### Check Pod's Volume Mount
```bash
kubectl describe pod my-pod                      # View volume mounts in the Pod
```

### Access Pod Shell
```bash
kubectl exec -it my-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete PVC, StorageClass, and Pod
```bash
kubectl delete pvc my-dynamic-pvc
kubectl delete storageclass my-dynamic-storage-class
kubectl delete pod my-pod
```

---

## ✅ Summary

- Created **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** for dynamic provisioning **declaratively** using YAML.
- Explored how to manage dynamic provisioning with `kubectl`.
- Key debugging tools:
  - `kubectl get pvc`
  - `kubectl describe pvc`
  - `kubectl describe storageclass`
  - `kubectl get pv`
  - `kubectl exec`
  - `kubectl delete`
- Dynamic provisioning allows Kubernetes to automatically provision Persistent Volumes when a PVC is created.

⚡ **Pro Tip**: Ensure that your cloud provider or infrastructure supports dynamic volume provisioning and that the correct provisioner is configured in your StorageClass.

