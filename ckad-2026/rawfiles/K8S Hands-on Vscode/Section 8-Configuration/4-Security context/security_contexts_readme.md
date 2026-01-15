
# Kubernetes Hands-on: Using Security Contexts

In this hands-on session, we will learn how to use **Security Contexts** in Kubernetes to manage the security settings for Pods and containers.  
We will explore the declarative method (using YAML files) and demonstrate how to configure and manage security contexts.

---

## 📌 1. Declarative Method (YAML)

We will first create a Pod with a Security Context using a YAML file inside **VS Code**.

### Example: `pod-with-security-context.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: my-secure-pod            # The unique name of the Pod
spec:                           # The specification of the Pod
  securityContext:               # Security context applied to the whole Pod
    runAsUser: 1000              # Set the user ID that the Pod should run as
    fsGroup: 2000                # Set the file system group ID
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    securityContext:            # Security context applied to the container
      allowPrivilegeEscalation: false  # Prevent privilege escalation
      runAsNonRoot: true         # Ensure the container runs as a non-root user
    ports:                       # Ports exposed by this container
    - containerPort: 80         # The application listens on port 80
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Pods.
- **kind: Pod** → Defines the object type.
- **metadata** → Includes Pod name (`my-secure-pod`).
- **securityContext** → Defines security settings for the Pod.
  - **runAsUser** → Sets the user ID that the Pod runs as.
  - **fsGroup** → Sets the file system group ID.
- **containers.securityContext** → Defines security settings for individual containers.
  - **allowPrivilegeEscalation** → Prevents privilege escalation in the container.
  - **runAsNonRoot** → Ensures the container runs as a non-root user.

### Commands:

```bash
kubectl apply -f pod-with-security-context.yaml    # Create the Pod with a security context
kubectl get pods                                  # Verify Pod status
kubectl describe pod my-secure-pod                # View Pod details
```

---

## 📌 2. Debugging and Troubleshooting Security Contexts

### View Pod's Security Context
```bash
kubectl get pod my-secure-pod -o yaml | grep securityContext
```
- Displays the security context of the Pod.

### Describe Pod
```bash
kubectl describe pod my-secure-pod
```
- Shows details of the Pod, including security context and any issues.

### Access Pod Shell
```bash
kubectl exec -it my-secure-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Pod
```bash
kubectl delete pod my-secure-pod
```

---

## ✅ Summary

- Created a **Pod with a Security Context** **declaratively** using YAML.
- Explored how to manage Security Contexts with `kubectl`.
- Key debugging tools:
  - `kubectl get pods`
  - `kubectl describe pod`
  - `kubectl exec`
  - `kubectl delete`
- Security contexts help ensure that Pods and containers run with the necessary privileges and security configurations.

⚡ **Pro Tip**: Use security contexts to enforce security best practices, such as running containers with non-root users and preventing privilege escalation.

