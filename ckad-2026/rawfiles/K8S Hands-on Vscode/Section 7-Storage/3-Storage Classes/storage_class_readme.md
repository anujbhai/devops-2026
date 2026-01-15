
# Kubernetes Hands-on: Using StorageClass

In this hands-on session, we will learn how to configure and use **StorageClass** in Kubernetes to define how storage resources are provisioned dynamically.  
We will explore the declarative method (using YAML files) and demonstrate how to create and manage StorageClass.

---

## 📌 1. Declarative Method (YAML)

We will first create a StorageClass using a YAML file inside **VS Code**.

### Example: `storage-class.yaml`

```yaml
### Example: `storage-class.yaml`

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: my-storage-class
provisioner: k8s.io/minikube-hostpath     # Works with Minikube
reclaimPolicy: Retain                     # Keep PV after PVC deletion
volumeBindingMode: Immediate              # Provision volume immediately

```

### Key Fields Explained
- **apiVersion: storage.k8s.io/v1** → Kubernetes API version for StorageClasses.
- **kind: StorageClass** → Defines the object type.
- **metadata** → Includes StorageClass name (my-storage-class).
- **provisioner** → Specifies the provisioner that will handle volume creation. In Minikube, we use k8s.io/minikube-hostpath which provisions local hostPath volumes.
- **reclaimPolicy**  → Defines what happens to the volume after the PVC is deleted (Retain means the volume is kept and data is preserved).
- **volumeBindingMode**  → Specifies when the volume is provisioned. Immediate provisions the volume as soon as the PVC is created, unlike WaitForFirstConsumer which delays provisioning until a Pod uses it.



### Commands:

```bash
kubectl apply -f storage-class.yaml       # Create the StorageClass
kubectl get storageclass                  # Verify StorageClass status
kubectl describe storageclass my-storage-class  # View StorageClass details
```

---

## 📌 2. Using StorageClass with PVC

### Example: `pvc-with-storage-class.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Persistent Volume Claims is "v1"
kind: PersistentVolumeClaim      # We are creating a resource of type PersistentVolumeClaim
metadata:                        # Metadata contains identifying information
  name: my-pvc                   # The unique name of the PersistentVolumeClaim
spec:                            # The specification of the PersistentVolumeClaim
  accessModes:                   # Access modes for the PersistentVolumeClaim
    - ReadWriteOnce              # Can be mounted as read-write by a single node
  resources:                     # The requested resources for the PersistentVolumeClaim
    requests:
      storage: 1Gi               # Requesting 1Gi of storage
  storageClassName: my-storage-class  # Reference to the StorageClass
```

### Key Fields Explained for PVC with StorageClass
- **storageClassName** → References the StorageClass (`my-storage-class`) to use for provisioning the Persistent Volume.

### Commands:

```bash
kubectl apply -f pvc-with-storage-class.yaml    # Create the Persistent Volume Claim with StorageClass
kubectl get pvc                                  # Verify PVC status
kubectl describe pvc my-pvc                      # View PVC details
```

---

### 📌 3 Create a Pod That Uses the Persistent Volume Claim

Now that our Persistent Volume Claim is ready, let’s attach it to a Pod.
We’ll use a simple BusyBox container to demonstrate the volume mount.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-demo
spec:
  containers:
  - name: app
    image: busybox
    command: ["/bin/sh"]
    args: ["-c", "sleep 3600"]
    volumeMounts:
    - mountPath: /mnt/data
      name: my-volume
  volumes:
  - name: my-volume
    persistentVolumeClaim:
      claimName: my-pvc
```
This Pod runs a BusyBox container that sleeps for one hour, giving us time to inspect it.
The volume from the claim my dash pvc is mounted at the path /mnt/data.

Apply this file:
```bash
kubectl apply -f pvc-demo.yaml
```

Then confirm that the Pod is running:
```bash
kubectl get pods
```

To verify that the volume is mounted correctly, enter the container:
```bash
kubectl exec -it pvc-demo -- sh
```

Inside the Pod, run:
```bash
df -h
```

You should see /mnt/data listed as a mounted volume, confirming that the Persistent Volume Claim is correctly attached.

Great it's working.

---

## 📌 4. Debugging and Troubleshooting StorageClass

### View StorageClass Details
```bash
kubectl describe storageclass my-storage-class      # View StorageClass details
```

### View PVC's StorageClass
```bash
kubectl describe pvc my-pvc                         # View the StorageClass used by the PVC
```



### clean-up StorageClass, PVC, and Pod
```bash

```

---

## ✅ Summary


- Created a **Storage Class** in Minikube using the hostpath provisioner.
- Created a Persistent Volume Claim that dynamically provisions a volume.
- Attached that claim to a BusyBox Pod to mount the volume 
- Verified storage persistence and cleanup behavior using the Retain policy.

**Storage Classes** provide a flexible and automated way to manage storage dynamically in Kubernetes , especially useful when deploying applications that need persistent data.
