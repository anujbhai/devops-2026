
# Kubernetes Hands-on: Using ConfigMaps

In this hands-on session, we will learn how to use **ConfigMaps** in Kubernetes to manage configuration data.  
We will explore the declarative method (using YAML files) and demonstrate how to create and manage ConfigMaps.

---

## 📌 1. Declarative Method (YAML)

We will first create a ConfigMap using a YAML file inside **VS Code**.

### Example: `configmap.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for ConfigMaps is "v1"
kind: ConfigMap                 # We are creating a resource of type ConfigMap
metadata:                        # Metadata contains identifying information
  name: my-configmap             # The unique name of the ConfigMap
data:                            # The actual configuration data (key-value pairs)
  config-key: myconfigvalue      # Key-value pair for the configuration
  app-mode: production           # Another key-value pair
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for ConfigMaps.
- **kind: ConfigMap** → Defines the object type.
- **metadata** → Includes ConfigMap name (`my-configmap`).
- **data** → The actual configuration data, where keys are the configuration names and values are the corresponding values.

### Commands:

```bash
kubectl apply -f configmap.yaml      # Create the ConfigMap
kubectl get configmaps               # Verify ConfigMap status
kubectl describe configmap my-configmap  # View ConfigMap details
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same ConfigMap using the **imperative method** with `kubectl` commands.

### Example:

```bash
kubectl create configmap my-configmap --from-literal=config-key=myconfigvalue --from-literal=app-mode=production
kubectl get configmaps
kubectl describe configmap my-configmap
```

Explanation:  
- `kubectl create configmap my-configmap` → Creates the ConfigMap named **my-configmap**.
- `--from-literal=config-key=myconfigvalue` → Creates a key-value pair in the ConfigMap.
- `kubectl describe configmap my-configmap` → Displays the details of the created ConfigMap.

---

## 📌 3. Using ConfigMaps in Pods

### Example: `pod-with-configmap.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: pod-with-configmap       # The unique name of the Pod
spec:                           # The specification of the Pod
  containers:                    # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    envFrom:               # Referencing the ConfigMap to inject environment variables
    - configMapRef:
        name: my-configmap      # Referencing the ConfigMap by name
    ports:                       # Ports exposed by this container
    - containerPort: 80         # The application listens on port 80
```

### Key Fields Explained for Pod with ConfigMap
- **envFrom** → Allows referencing a ConfigMap and injecting its keys as environment variables into the container.
- **configMapRef.name** → The name of the ConfigMap (`my-configmap`) to be used.

### Commands:

```bash
kubectl apply -f pod-with-configmap.yaml    # Create the Pod with environment variables from ConfigMap
kubectl get pods                        # Verify Pod status
kubectl exec -it pod-with-configmap -- printenv  # Check environment variables inside the Pod
```

---

## 📌 4. Debugging and Troubleshooting ConfigMaps

### View ConfigMap Data
```bash
kubectl get configmap my-configmap -o yaml
```
- This command shows the ConfigMap data in YAML format.

### Access Pod Shell
```bash
kubectl exec -it pod-with-configmap -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete ConfigMap and Pod
```bash
kubectl delete configmap my-configmap
kubectl delete pod pod-with-configmap
```

---

## ✅ Summary

- Created a **ConfigMap** **declaratively** using YAML.
- Explored how to manage ConfigMaps with `kubectl`.
- Key debugging tools:
  - `kubectl get configmaps`
  - `kubectl describe configmap`
  - `kubectl exec`
  - `kubectl delete`
- ConfigMaps allow you to store configuration data separately from Pods, and inject this data into Pods as environment variables.

⚡ **Pro Tip**: Use ConfigMaps to store non-sensitive configuration data and separate application configuration from code.

