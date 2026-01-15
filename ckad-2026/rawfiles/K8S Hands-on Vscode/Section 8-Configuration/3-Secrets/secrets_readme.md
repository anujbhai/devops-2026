
# Kubernetes Hands-on: Using Secrets

In this hands-on session, we will learn how to use **Secrets** in Kubernetes to manage sensitive data such as passwords, tokens, and certificates.  
We will explore the declarative method (using YAML files) and demonstrate how to create and manage secrets.

---

## 📌 1. Declarative Method (YAML)

We will first create a Secret using a YAML file inside **VS Code**.

### Example: `secret.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Secrets is "v1"
kind: Secret                     # We are creating a resource of type Secret
metadata:                        # Metadata contains identifying information
  name: my-secret                # The unique name of the Secret
type: Opaque                     # Default Secret type for generic data
data:                            # The actual secret data (encoded in base64)
  username: bXl1c2Vy              # base64 encoded "myuser"
  password: bXlwYXNzd29yZA==      # base64 encoded "mypassword"
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Secrets.
- **kind: Secret** → Defines the object type.
- **metadata** → Includes Secret name (`my-secret`).
- **type: Opaque** → Specifies the type of the Secret (default type for generic secrets).
- **data** → The actual secret data, encoded in base64.
  - **username** → The secret data, base64 encoded.
  - **password** → Another secret data, base64 encoded.

### Commands:

```bash
kubectl apply -f secrets.yaml      # Create the Secret
kubectl get secrets               # Verify Secret status
kubectl describe secret my-secret # View Secret details
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same Secret using the **imperative method** with `kubectl` commands.

### Example:

```bash
kubectl create secret2 generic my-secret --from-literal=username=myuser --from-literal=password=mypassword
kubectl get secrets
kubectl describe secret my-secret
```

Explanation:  
- `kubectl create secret generic my-secret` → Creates a Secret named **my-secret**.
- `--from-literal=username=myuser` → Creates the secret with the literal value for the username.
- `kubectl describe secret my-secret` → Displays the details of the created Secret.

---

## 📌 3. Using Secrets in Pods

### Example: 

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: pod-with-secret         # The unique name of the Pod
spec:                            # The specification of the Pod
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    envFrom:           # Referencing the Secret to inject environment variables
    - secretRef:
        name: my-secret         # Referencing the Secret by name
```

### Key Fields Explained for Pod with Secret
- **envFrom** → Allows referencing a Secret and injecting its keys as environment variables into the container.
- **secretRef.name** → The name of the Secret (`my-secret`) to be used.

### Commands:

```bash
kubectl apply -f secrets.yaml    # Create the Pod with environment variables from Secret
kubectl get pods                        # Verify Pod status
kubectl exec -it pod-with-secret -- printenv  # Check environment variables inside the Pod
```

---

## 📌 4. Debugging and Troubleshooting Secrets

### View Secret Data
```bash
kubectl get secret my-secret -o yaml
```
- This command shows the Secret data in base64-encoded form.

### Decode Secret Data
```bash
echo <base64-encoded-data> | base64 --decode
```
- Decodes the base64-encoded secret data to its original form.

### Access Pod Shell
```bash
kubectl exec -it pod-with-secret -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Secret and Pod
```bash
kubectl delete secret my-secret
kubectl delete pod pod-with-secret
```

---

## ✅ Summary

- Created a **Secret** **declaratively** using YAML.
- Explored how to manage Secrets with `kubectl`.
- Key debugging tools:
  - `kubectl get secrets`
  - `kubectl describe secret`
  - `kubectl exec`
  - `kubectl delete`
- Secrets allow you to securely store sensitive data like passwords and API tokens in Kubernetes.

⚡ **Pro Tip**: Always encode sensitive data (like passwords and tokens) in base64 format before storing them in a Secret. Avoid placing secrets directly in YAML files.

