
### Kubernetes Hands-on: Pod Security Standards (Minikube)

We will demonstrate Pod Security Standards using Minikube, showing both compliant and non-compliant Pods.

## 📌 1. Create a namespace with a Restricted security policy
```bash
kubectl create namespace secure-ns
kubectl label namespace secure-ns pod-security.kubernetes.io/enforce=restricted
```

Verify the label:
```bash
kubectl get namespace secure-ns --show-labels
```
## 📌 2. Pod Compliant with Restricted Policy

Create restricted-demo.yaml:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: restricted-demo
  namespace: secure-ns
spec:
  containers:
  - name: demo
    image: busybox
    command: ["/bin/sh"]
    args: ["-c", "sleep 3600"]
    securityContext:
      runAsNonRoot: true
      runAsUser: 1000
      allowPrivilegeEscalation: false
      capabilities:
        drop: ["ALL"]
      seccompProfile:
        type: RuntimeDefault
```

Apply and check:
```bash
kubectl apply -f restricted-demo.yaml
kubectl get pods -n secure-ns
```

✅ This Pod runs successfully.
## 📌 3. Pod Violating Restricted Policy

Create violating-demo.yaml:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: violating-demo
  namespace: secure-ns
spec:
  containers:
  - name: nginx
    image: nginx
    securityContext:
      privileged: true   # This violates the Restricted policy
```

Try to apply it:
```bash
kubectl apply -f violating-demo.yaml
```

❌ You will get an error like:

Error from server (Forbidden): pods "violating-demo" is forbidden: violates PodSecurity "restricted:latest": ...


This demonstrates what is forbidden under the Restricted policy.

📌 4. Debugging and cleanup
Check Pod events:

```bash
kubectl get events -n secure-ns --sort-by=.metadata.creationTimestamp
```
```bash
Delete the Pods and namespace:
kubectl delete pod restricted-demo -n secure-ns
kubectl delete namespace secure-ns
```
✅ Summary

Showed a Pod that complies and a Pod that violates the Restricted policy.

Key security controls: runAsNonRoot, allowPrivilegeEscalation, capabilities, privileged, seccompProfile.

Violating Pods are rejected by the API server.

⚡ **Pro Tip**: Always test Restricted policies with simple Pods (e.g., busybox) to see the enforcement in action.