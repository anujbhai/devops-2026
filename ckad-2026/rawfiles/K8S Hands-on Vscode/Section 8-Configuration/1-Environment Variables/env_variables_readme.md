
# Kubernetes Hands-on: Using Environment Variables

In this hands-on session, we will learn how to use **Environment Variables** in Kubernetes.  
We will explore the declarative method (using YAML files) and demonstrate how to manage environment variables in Pods and Services.

---

## 📌 1. Declarative Method (YAML)

We will first create a Pod with environment variables using a YAML file inside **VS Code**.

### Example: `pod-with-env-vars.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Pods is "v1"
kind: Pod                       # We are creating a resource of type Pod
metadata:                        # Metadata contains identifying information
  name: my-pod                  # The unique name of the Pod
spec:                           # The specification of the Pod
  containers:                   # List of containers inside this Pod
  - name: nginx-container       # Name of the first container
    image: nginx:latest         # Container image to use (nginx, latest tag)
    env:                        # List of environment variables
    - name: ENV_VAR_1           # Name of the environment variable
      value: "value1"           # Value of the environment variable
    - name: ENV_VAR_2           # Another environment variable
      valueFrom:
        configMapKeyRef:
          name: my-config-map   # Reference to a ConfigMap to get the value
          key: config-key       # Key in the ConfigMap to fetch
    ports:                       # Ports exposed by this container
    - containerPort: 80         # The application listens on port 80
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Pods.
- **kind: Pod** → Defines the object type.
- **metadata** → Includes Pod name (`my-pod`).
- **spec** → Defines desired state.
  - **env** → Specifies environment variables for the container.
  - **name** → Environment variable name.
  - **value** → Direct value for the environment variable.
  - **valueFrom** → Fetches the environment variable value from a ConfigMap.

### Commands:

```bash
kubectl apply -f pod-with-env-vars.yaml    # Create the Pod with environment variables
kubectl get pods                           # Verify Pod status
kubectl exec -it my-pod -- printenv        # Check environment variables inside the Pod
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same Pod with environment variables using the **imperative method** with `kubectl` commands.

### Example:

```bash
kubectl run my-pod --image=nginx:latest --env="ENV_VAR_1=value1" --env="ENV_VAR_2=value2"

kubectl get pods
kubectl exec -it my-pod -- printenv
```

Explanation:  
- `kubectl run my-pod` → Creates a Pod named **my-pod**.
- `--env="ENV_VAR_1=value1"` → Defines environment variables directly from the command line.
- `kubectl exec -it my-pod -- printenv` → Checks environment variables inside the Pod.

---

## 📌 3. Debugging and Troubleshooting Environment Variables

### View Pod's Environment Variables
```bash
kubectl exec -it my-pod -- printenv
```
- Displays the environment variables set inside the container.

### Describe Pod
```bash
kubectl describe pod my-pod
```
- Shows details of the Pod, including the environment variables.

### Access Pod Shell
```bash
kubectl exec -it my-pod -- /bin/bash
```
- Opens an interactive shell inside the specified Pod.

### Delete Pod
```bash
kubectl delete pod my-pod
```

---

## ✅ Summary

- Created a **Pod with environment variables** **declaratively** using YAML.
- Explored how to set environment variables using `kubectl`.
- Key debugging tools:
  - `kubectl get pods`
  - `kubectl describe pod`
  - `kubectl exec`
  - `kubectl delete`
- Environment variables allow you to pass configuration to containers and manage Pod behavior.

⚡ **Pro Tip**: Use ConfigMaps or Secrets for sensitive data like passwords or API keys to avoid exposing them in your YAML files.

