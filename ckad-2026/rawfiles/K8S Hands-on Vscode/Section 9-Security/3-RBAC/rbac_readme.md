
# Kubernetes Hands-on: Using Role-Based Access Control (RBAC)

In this hands-on session, we will learn how to configure **Role-Based Access Control (RBAC)** in Kubernetes.  
We will explore how to define Roles and RoleBindings to control access to resources within the cluster.

---

## 📌 1. Declarative Method (YAML)

We will first create a **Role** and **RoleBinding** using YAML files inside **VS Code**.

### Example: `role.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1   # The Kubernetes API version for Role is "rbac.authorization.k8s.io/v1"
kind: Role                                # We are creating a resource of type Role
metadata:                                  # Metadata contains identifying information
  name: pod-reader                         # The unique name of the Role
  namespace: default                       # The namespace in which the role is applied
rules:                                     # Defines the permissions for the role
- apiGroups: [""]                          # The empty string means the core API group (Pods, Services, etc.)
  resources: ["pods"]                      # The resource type (Pods)
  verbs: ["get", "list"]                   # The verbs (actions) allowed (get and list Pods)
```

### Example: `rolebinding.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1    # The Kubernetes API version for RoleBinding is "rbac.authorization.k8s.io/v1"
kind: RoleBinding                           # We are creating a resource of type RoleBinding
metadata:                                   # Metadata contains identifying information
  name: pod-reader-binding                  # The unique name of the RoleBinding
  namespace: default                        # The namespace in which the RoleBinding is applied
subjects:                                   # Defines the users or service accounts to whom the role is assigned
- kind: User                                # The type of subject (could be User, ServiceAccount, etc.)
  name: developer                           # The name of the subject (e.g., "developer")
  apiGroup: rbac.authorization.k8s.io       # The API group for RBAC subjects
roleRef:                                    # References the role that is being assigned
  kind: Role                                # The kind of the referenced role (could be Role or ClusterRole)
  name: pod-reader                          # The name of the role being assigned
  apiGroup: rbac.authorization.k8s.io       # The API group for the referenced role
```

### Key Fields Explained
- **apiVersion: rbac.authorization.k8s.io/v1** → Kubernetes API version for RBAC.
- **kind: Role** → Defines the object type for Role.
- **kind: RoleBinding** → Defines the object type for RoleBinding.
- **metadata** → Includes the name of the Role and RoleBinding (`pod-reader`, `pod-reader-binding`).
- **subjects** → Defines who the Role is assigned to (e.g., a user, service account).
- **roleRef** → References the Role that is assigned to the subject.

### Commands:

```bash
kubectl apply -f role.yaml              # Create the Role
kubectl apply -f rolebinding.yaml # Create the RoleBinding
kubectl get role                        # Verify Role status
kubectl get rolebinding                 # Verify RoleBinding status
kubectl describe rolebinding pod-reader-binding  # View RoleBinding details
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same **Role** and **RoleBinding** using the **imperative method** with `kubectl` commands.

### Example:

#### Create Role:

```bash
kubectl create role pod-reader2 --verb=get,list --resource=pods --namespace=default
kubectl get roles -n default
```

#### Create RoleBinding:

```bash
kubectl create rolebinding pod-reader-binding2 --role=pod-reader --user=developer --namespace=default
kubectl get rolebindings -n default
```

Explanation:  
- `kubectl create role` → Creates a Role with specified verbs (`get`, `list`) and resources (`pods`).
- `kubectl create rolebinding` → Creates a RoleBinding that binds a user (`developer`) to the `pod-reader` role.

---

## 📌 3. Debugging and Troubleshooting RBAC

### View Role and RoleBinding Details
```bash
kubectl describe role pod-reader             # View Role details
kubectl describe rolebinding pod-reader-binding  # View RoleBinding details
```

### Check Permissions for User
```bash
kubectl auth can-i get pods --as=developer --namespace=default
```
- This command checks whether the `developer` user can perform the `get` operation on Pods.

### Access Pod Shell
```bash
kubectl exec -it my-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Role and RoleBinding
```bash
kubectl delete role pod-reader
kubectl delete rolebinding pod-reader-binding
kubectl delete role pod-reader2
kubectl delete rolebinding pod-reader-binding2
```

---

## ✅ Summary

- Created **Roles** and **RoleBindings** **declaratively** using YAML.
- Explored how to manage RBAC resources with `kubectl`.
- Key debugging tools:
  - `kubectl get roles`
  - `kubectl get rolebindings`
  - `kubectl describe role`
  - `kubectl describe rolebinding`
  - `kubectl auth can-i`
  - `kubectl exec`
  - `kubectl delete`
- RBAC provides a way to manage user permissions in Kubernetes, ensuring that only authorized users can access specific resources.

⚡ **Pro Tip**: RBAC is essential for securing your Kubernetes cluster. Always follow the principle of least privilege by granting users only the permissions they need.

