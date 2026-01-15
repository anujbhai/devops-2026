# Kubernetes Hands-on: Using Persistent Volumes (PV), Persistent Volume Claims (PVC), and Pods

In this hands-on session, we will learn how to configure Persistent Volumes (PV), Persistent Volume Claims (PVC), and create a Pod that uses the PVC.
We will explore the declarative method (using YAML files) and demonstrate how to configure, mount, and verify persistent storage usage.

# 📌 1. Declarative Method (YAML)

We will first create a Persistent Volume (PV) and Persistent Volume Claim (PVC) using YAML files.

Example: pv.yaml
```yaml
apiVersion: v1                  # The Kubernetes API version for Persistent Volumes is "v1"
kind: PersistentVolume           # We are creating a resource of type PersistentVolume
metadata:                        # Metadata contains identifying information
  name: my-pv                    # The unique name of the PersistentVolume
spec:                            # The specification of the PersistentVolume
  capacity:                      # The storage capacity of the PersistentVolume
    storage: 1Gi                 # 1Gi of storage
  accessModes:                   # Access modes of the PersistentVolume
    - ReadWriteOnce              # Can be mounted as read-write by a single node
  persistentVolumeReclaimPolicy: Retain    # The policy for when the volume is released
  hostPath:                      # A simple hostPath to simulate persistent storage
    path: /mnt/data/pvdata        # Path to the volume on the node

Example: pvc.yaml
apiVersion: v1              # API version for core Kubernetes resources
kind: PersistentVolumeClaim # The resource type being created is a PVC

metadata:
  name: my-pvc              # Name of the PVC, used to reference it in Pods

spec:
  accessModes:
    - ReadWriteOnce         # The volume can be mounted as read-write by a single node
  resources:
    requests:
      storage: 1Gi          # The amount of storage being requested from a PersistentVolume
```
Commands:
```bash
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl get pv
kubectl get pvc
```

# 📌 2. Debugging and Troubleshooting PV and PVC

View PV and PVC Details
```bash
kubectl describe pv my-pv
kubectl describe pvc my-pvc
```

# 📌 3. Create a Pod That Uses the PVC

Once the PV and PVC are bound, we can create a Pod that mounts the PVC to use persistent storage.

Example: pod-using-pvc.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pvc-pod               # The name of the Pod
spec:
  containers:
  - name: app-container          # The main container in the Pod
    image: nginx                 # Using nginx as an example container
    volumeMounts:
    - name: storage-volume       # Name of the volume mount
      mountPath: /usr/share/nginx/html   # Path inside the container where storage is mounted
  volumes:
  - name: storage-volume         # Volume name referenced by the container
    persistentVolumeClaim:
      claimName: my-pvc          # PVC that this Pod uses
```
Commands to Deploy and Verify
```bash
kubectl apply -f pod-using-pvc.yaml        # Create the Pod
kubectl get pods                           # Check Pod status
kubectl describe pod my-pvc-pod            # Verify PVC is mounted
```
Test the Volume Mount Inside the Pod
```bash
kubectl exec -it my-pvc-pod -- /bin/bash   # Access the Pod shell
echo "Hello Persistent Storage" > /usr/share/nginx/html/index.html
cat /usr/share/nginx/html/index.html       # Verify file is written
```

If you delete and recreate the Pod, the file should persist because the data is stored in the Persistent Volume.

### clean up
```bash
kubectl delete pod my-pvc-pod
kubectl delete pv my-pv
kubectl delete pvc my-pvc
```
✅ Summary

Created Persistent Volumes (PV) and Persistent Volume Claims (PVC) declaratively using YAML.

- Created a Pod that uses the PVC to mount persistent storage.
- Verified data persistence across Pod restarts.
- Learned debugging commands:
- `kubectl get pv, kubectl get pvc`
- `kubectl describe pv, kubectl describe pvc`
- `kubectl exec -it`

Persistent Volumes ensure that application data remains available even if Pods are recreated.

⚡ **Pro Tip**:
Always ensure your PVC and PV have matching access modes and storage capacities before creating Pods that depend on them.