
# Kubernetes Hands-on: Creating a Service of Type NodePort

In this hands-on session, we will create a **Service of Type NodePort** in Kubernetes.  
We will explore the declarative method (using a YAML file) and learn how to manage NodePort services using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We will first create a NodePort service using a YAML file inside **VS Code**.

### Example: `service-nodeport.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Services is "v1"
kind: Service                    # We are creating a resource of type Service
metadata:                        # Metadata contains identifying information
  name: my-nodeport-service      # The unique name of the Service
spec:                             # The specification of the Service
  selector:                       # Selector defines the Pods that this Service will route traffic to
    app: nginx                    # The label selector to find the Pods (e.g., "app=nginx")
  ports:                           # The ports exposed by this Service
    - protocol: TCP
      port: 80                     # Port exposed by the Service
      targetPort: 80               # Port on the Pod that the Service will forward to
      nodePort: 30080              # NodePort to expose the service externally
  type: NodePort                  # Service type is NodePort
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Services.
- **kind: Service** → Defines the object type.
- **metadata** → Includes Service name (`my-nodeport-service`).
- **spec** → Defines desired state.
  - **selector** → Defines the label selector to find the Pods (e.g., `app: nginx`).
  - **ports** → Specifies the ports exposed by the Service (`80` here).
  - **nodePort** → Defines the external port (e.g., `30080`) that Kubernetes will expose.
  - **type: NodePort** → Specifies the Service type as NodePort.

### Commands:

```bash
kubectl apply -f NodePort.yaml    # Create the NodePort Service


### Create a pod to which service will send traffic :

kubectl run my-pod --image=nginx --labels="app=nginx"

```

---

## 📌 2. Imperative Method (Command Line)

We can also create the same NodePort service using the **imperative method** with `kubectl` commands.

### Example:

```bash
kubectl expose pod my-pod --port=80 --target-port=80 --name=my-nodeport-service2 --type=NodePort

```

Explanation:  
- `kubectl expose pod my-pod` → Exposes the Pod as a Service.
- `--port=80` → The port exposed by the Service.
- `--target-port=80` → The target port on the Pod.
- `--name=my-nodeport-service` → Specifies the Service name.
- `--type=NodePort` → Specifies the Service type as NodePort.
- `--node-port=30080` → Specifies the external port to access the service.

---

## 📌 3. Debugging and Troubleshooting Services

### List Services
```bash
kubectl get svc -n default
```

### Describe Service
```bash
kubectl describe svc my-nodeport-service
```
- Shows details of the Service, including selector, ports, and associated endpoints.

### test the service NodPort

1- From inside the cluster (always works):

kubectl run busybox --rm -it --image=busybox -- /bin/sh


Then inside the pod shell:

wget -qO- http://my-nodeport-service:80
you will get a message :
<h1>Welcome to nginx!</h1>
this means that the service forwarded traffic to the nginx pod 

2- From your host (external access):

Use the Minikube IP dynamically:

minikube service my-nodeport-service


This opens a browser or forwards traffic properly, bypassing VM networking issues.




### Delete Service
```bash
kubectl delete service my-nodeport-service
```

### Delete Pods
```bash
kubectl delete pod my-pod
```
- Deleting Pods will cause the Service to route traffic to other Pods with matching labels.

---

## ✅ Summary

- Created a **NodePort Service** **declaratively** using YAML.
- Explored how to manage NodePort services with `kubectl`.
- Key debugging tools:
  - `kubectl get services`
  - `kubectl describe service`
  - `kubectl exec`
  - `kubectl delete`
- NodePort services expose Pods on an external port, making them accessible outside the cluster.

⚡ **Pro Tip**: NodePort services are useful for exposing applications externally when you don't have an ingress controller set up.

