
# Kubernetes Hands-on: Creating a Service of Type ClusterIP

In this hands-on session, we will create a **Service of Type ClusterIP** in Kubernetes.  
We will explore the declarative method (using a YAML file) and learn how to manage ClusterIP services using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We will first create a ClusterIP service using a YAML file inside **VS Code**.

### Example: `ClusterIP.yaml`

```yaml
# ClusterIP.yaml
apiVersion: v1                  # Kubernetes API version for Service
kind: Service                   # Resource type: Service

metadata:
  name: my-clusterip-service    # Unique name of the Service

spec:
  selector:
    app: nginx                  # Selects Pods with label app=nginx to route traffic to

  ports:
    - protocol: TCP             # Network protocol used by the Service
      port: 80                  # The port exposed by the Service within the cluster
      targetPort: 80            # The port on the container that the Service maps to

  type: ClusterIP       # Service type ClusterIP allows internal access only

```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Services.
- **kind: Service** → Defines the object type.
- **metadata** → Includes Service name (`my-clusterip-service`).
- **spec** → Defines desired state.
  - **selector** → Defines the label selector to find the Pods (e.g., `app: nginx`).
  - **ports** → Specifies the ports exposed by the Service (`80` here).
  - **type: clusterIP** →Service type ClusterIP allows internal access only

### Commands:

```bash
kubectl apply -f ClusterIP.yaml    # Create the ClusterIP Service

### Create a pod to which service will send traffic :

kubectl run my-pod --image=nginx --labels="app=nginx"



---

## 📌 2. Imperative Method (Command Line)

### Example:

```bash
kubectl expose pod my-pod --port=80 --target-port=80 --name=my-clusterip-service2 --type=ClusterIP


Explanation:  
- `kubectl expose pod my-pod` → Exposes the Pod as a Service.
- `--port=80` → The port exposed by the Service.
- `--target-port=80` → The target port on the Pod.
- `--name-my-clusterip-service` → name of the service.
- `--type=ClusterIP` → Specifies the Service type as ClusterIP.
```


---

## 📌 3. Debugging and Troubleshooting Services

### List Services
```bash
kubectl get services -n default
```

### Describe Service
```bash
kubectl describe service my-clusterip-service
```
- Shows details of the Service, including selector, ports, and associated endpoints.

## get endpoints :
kubectl get endpoints my-clusterip-service
  --> * Verifies which Pods are selected by the Service.



## 📌 4. Test the Service from another Pod

We can run a temporary Pod to access the Service:
```bash
kubectl run busybox --rm -it --image=busybox -- /bin/sh


Inside the Pod shell we execute the command :

wget -qO- http://my-clusterip-service


This sends an HTTP request to the ClusterIP Service.

You should see the HTML from NGINX, demonstrating that the Service forwards traffic to the Pod.


### Delete Service
```bash
kubectl delete service my-clusterip-service
```

### Delete Pods
```bash
kubectl delete pod my-pod
```
- Deleting Pods will cause the Service to route traffic to other Pods with matching labels.

---

## ✅ Summary

- Created a **ClusterIP Service** **declaratively** using YAML.
- Explored how to manage ClusterIP services with `kubectl`.
- Key debugging tools:
  - `kubectl get services`
  - `kubectl describe service`
  - `kubectl exec`
  - `kubectl delete`
- ClusterIP services route traffic to Pods based on labels, only accessible within the cluster.

⚡ **Pro Tip**: ClusterIP services are the default service type in Kubernetes and are used for internal communication within the cluster.

