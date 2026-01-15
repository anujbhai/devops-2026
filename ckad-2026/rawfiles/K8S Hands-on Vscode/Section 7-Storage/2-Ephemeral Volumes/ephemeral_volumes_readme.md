
# Kubernetes Hands-on: Using Ephemeral Volumes

In this hands-on session, we will learn how to configure **Ephemeral Volumes** in Kubernetes.  
We will explore the declarative method (using YAML files) and demonstrate how to configure and manage Ephemeral Volumes.

---

## 📌 1. Declarative Method (YAML)

We will first create a Pod with an Ephemeral Volume using a YAML file inside **VS Code**.

### Example: `pod-with-ephemeral-volume.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: my-ephemeral-pod         # The unique name of the Pod
spec:                           # The specification of the Pod
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    volumeMounts:                # Mount the ephemeral volume to the container
    - mountPath: /tmp            # Mount path for the ephemeral volume inside the container
      name: ephemeral-storage    # Name of the ephemeral volume to mount
  volumes:                       # Define the volumes used by the Pod
  - name: ephemeral-storage     # Name of the ephemeral volume
    emptyDir: {}                 # An ephemeral volume type (emptyDir)
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Pods.
- **kind: Pod** → Defines the object type.
- **metadata** → Includes Pod name (`my-ephemeral-pod`).
- **spec** → Defines desired state.
  - **containers.volumeMounts** → Mounts the ephemeral volume inside the container at the specified `mountPath`.
  - **volumes.emptyDir** → Defines an ephemeral volume that stores data temporarily as long as the Pod is running. The `emptyDir` type means the volume is stored in memory or on disk and is deleted once the Pod terminates.

### Commands:

```bash
kubectl apply -f pod-with-ephemeral-volume.yaml    # Create the Pod with an ephemeral volume
kubectl get pods                                  # Verify Pod status
kubectl describe pod my-ephemeral-pod             # View Pod details
```

---

## 📌 2. Debugging and Troubleshooting Ephemeral Volumes

### View Pod's Volume Configuration
```bash
kubectl describe pod my-ephemeral-pod | grep -A 10 'Volumes'  # View volume details in the Pod
```

# Step 1 :  Access Pod Shell
```bash
kubectl exec -it my-ephemeral-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.


Inside the Pod shell:
```bash
echo "Hello from ephemeral volume!" > /tmp/test-file
cat /tmp/test-file
```

You’ll see:  'Hello from ephemeral volume!'


✅ This confirms that the /tmp directory (mounted as emptyDir) is working.

Exit the Pod: Exit

# Step 3 — Verify File Exists (while Pod is alive)

You can confirm the file from outside the Pod:
```bash
kubectl exec -it my-ephemeral-pod -- cat /tmp/test-file
```

It should still print: ' Hello from ephemeral volume! '

# Step 4 — Delete the Pod

Now, delete the Pod completely:
```bash
kubectl delete pod my-ephemeral-pod
```

# Step 5 — Recreate the Pod with the Same YAML
```bash
kubectl apply -f pod-with-ephemeral-volume.yaml
```

Once it’s running again:
```bash
kubectl exec -it my-ephemeral-pod -- cat /tmp/test-file
```
You’ll get:

cat: /tmp/test-file: No such file or directory


✅ This clearly proves that the emptyDir volume was ephemeral — the data is gone because the Pod was recreated.



### clean up
```bash
kubectl delete pod my-ephemeral-pod
```

---

## ✅ Summary

- Created a **Pod with an Ephemeral Volume** **declaratively** using YAML.
- Explored how to manage Ephemeral Volumes with `kubectl`.
- Key debugging tools:
  - `kubectl get pods`
  - `kubectl describe pod`
  - `kubectl exec`
  - `kubectl delete`
- Ephemeral Volumes are useful for temporary data storage that does not need to persist across Pod restarts.

⚡ **Pro Tip**: Use Ephemeral Volumes when you need temporary storage for your Pods, such as cache or temporary files that don't need to persist beyond the lifecycle of the Pod.

