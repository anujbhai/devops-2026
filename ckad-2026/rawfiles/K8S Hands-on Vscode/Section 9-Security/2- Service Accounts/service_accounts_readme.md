
# Kubernetes Hands-on: Using Service Accounts

In this hands-on session, we will learn how to configure and use **Service Accounts** in Kubernetes.  
We will explore the declarative method (using YAML files) and demonstrate how to create and manage Service Accounts.

---

## 📌 1. Declarative Method (YAML)

We will first create a **ServiceAccount** using a YAML file inside **VS Code**.

### Example: `service-account.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for ServiceAccounts is "v1"
kind: ServiceAccount            # We are creating a resource of type ServiceAccount
metadata:                        # Metadata contains identifying information
  name: my-service-account      # The unique name of the ServiceAccount
  annotations:
    kubernetes.io/service-account.name: my-service-account
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Service Accounts.
- **kind: ServiceAccount** → Defines the object type.
- **metadata** → Includes ServiceAccount name (`my-service-account`).
- **annotations** → Optional metadata, such as the name of the ServiceAccount.

### Commands:

```bash
kubectl apply -f service-account.yaml    # Create the ServiceAccount
kubectl get serviceaccounts              # Verify ServiceAccount status
kubectl describe serviceaccount my-service-account  # View ServiceAccount details
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same **ServiceAccount** using the **imperative method** with `kubectl` commands.

### Example:

```bash
kubectl create serviceaccount my-service-account2
kubectl get serviceaccounts
kubectl describe serviceaccount my-service-account
```

Explanation:  
- `kubectl create serviceaccount my-service-account` → Creates a ServiceAccount named **my-service-account**.
- `kubectl describe serviceaccount my-service-account` → Displays the details of the created ServiceAccount.

---

## 📌 3. Using Service Account with Pods

### Example: `pod-with-service-account.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: pod-with-service-account # The unique name of the Pod
spec:                           # The specification of the Pod
  serviceAccountName: my-service-account  # Associate the Pod with a ServiceAccount
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    ports:                       # Ports exposed by this container
    - containerPort: 80         # The application listens on port 80
```

### Key Fields Explained for Pod with ServiceAccount
- **serviceAccountName** → Specifies the ServiceAccount that the Pod will use.

### Commands:

```bash
kubectl apply -f pod-with-service-account.yaml    # Create the Pod with a ServiceAccount
kubectl get pods                                  # Verify Pod status
kubectl describe pod pod-with-service-account     # View Pod details
```

---

## 📌 4. Debugging and Troubleshooting Service Accounts

### View ServiceAccount Details
```bash
kubectl describe serviceaccount my-service-account    # View the ServiceAccount details
```

### Access Pod Shell
```bash
kubectl exec -it pod-with-service-account -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete ServiceAccount and Pod
```bash
kubectl delete serviceaccount my-service-account
kubectl delete pod pod-with-service-account
```

---

## ✅ Summary

- Created a **ServiceAccount** **declaratively** using YAML.
- Explored how to manage ServiceAccounts with `kubectl`.
- Key debugging tools:
  - `kubectl get serviceaccounts`
  - `kubectl describe serviceaccount`
  - `kubectl exec`
  - `kubectl delete`
- ServiceAccounts provide a way for Pods to authenticate to the Kubernetes API server and access resources.

⚡ **Pro Tip**: ServiceAccounts are used in conjunction with Role-Based Access Control (RBAC) to manage access to Kubernetes resources.

