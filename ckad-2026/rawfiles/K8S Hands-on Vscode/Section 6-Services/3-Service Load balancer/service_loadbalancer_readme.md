
# Kubernetes Hands-on: Creating a Service of Type LoadBalancer

In this hands-on session, we will create a **Service of Type LoadBalancer** in Kubernetes.  
We will explore the declarative method (using a YAML file) and learn how to manage LoadBalancer services using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We will first create a LoadBalancer service using a YAML file inside **VS Code**.

### Example: `service-loadbalancer.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Services is "v1"
kind: Service                    # We are creating a resource of type Service
metadata:                        # Metadata contains identifying information
  name: my-loadbalancer-service  # The unique name of the Service
spec:                             # The specification of the Service
  selector:                       # Selector defines the Pods that this Service will route traffic to
    app: nginx                    # The label selector to find the Pods (e.g., "app=nginx")
  ports:                           # The ports exposed by this Service
    - protocol: TCP
      port: 80                     # Port exposed by the Service
      targetPort: 80               # Port on the Pod that the Service will forward to
  type: LoadBalancer               # Service type is LoadBalancer
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Services.
- **kind: Service** → Defines the object type.
- **metadata** → Includes Service name (`my-loadbalancer-service`).
- **spec** → Defines desired state.
  - **selector** → Defines the label selector to find the Pods (e.g., `app: nginx`).
  - **ports** → Specifies the ports exposed by the Service (`80` here).
  - **type: LoadBalancer** → Specifies the Service type as LoadBalancer.

### Commands:

```bash
kubectl apply -f loadbalancer.yaml    # Create the LoadBalancer Service

### Create a pod to which service will send traffic :

kubectl run my-pod --image=nginx --labels="app=nginx"

---

## 📌 2. Imperative Method (Command Line)

We can also create the same LoadBalancer service using the **imperative method** with `kubectl` commands.

### Example:

```bash
kubectl expose pod my-pod --port=80 --target-port=80 --name=my-loadbalancer-service2 --type=LoadBalancer


Explanation:  
- `kubectl expose pod my-pod` → Exposes the Pod as a Service.
- `--port=80` → The port exposed by the Service.
- `--target-port=80` → The target port on the Pod.
- `--name=my-loadbalancer-service` → Specifies the Service name.
- `--type=LoadBalancer` → Specifies the Service type as LoadBalancer.
```
---

### 📌 3. Debugging and Troubleshooting Services

### List Services
```bash
kubectl get services -n default
```

### Describe Service
```bash
kubectl describe service my-loadbalancer-service
```
- Shows details of the Service, including selector, ports, and associated endpoints.

###  Testing the loadbalancer inside the cluster

In a real cloud environment (AWS, GCP, Azure, DigitalOcean, etc.), a Service of type LoadBalancer will provision an external cloud load balancer. But in Minikube, there is no cloud provider,

that's why we are going to  test it internally using the command :

kubectl run busybox --rm -it --image=busybox -- /bin/sh


Inside the pod shell:

wget -qO- http://my-loadbalancer-service:80



### Delete Service
```bash
kubectl delete service my-loadbalancer-service
```

### Delete Pods
```bash
kubectl delete pod my-pod
```
- Deleting Pods will cause the Service to route traffic to other Pods with matching labels.

---

## ✅ Summary

- Created a **LoadBalancer Service** **declaratively** using YAML.
- Explored how to manage LoadBalancer services with `kubectl`.
- Key debugging tools:
  - `kubectl get services`
  - `kubectl describe service`
  - `kubectl exec`
  - `kubectl delete`
- LoadBalancer services route traffic externally, making them accessible outside the cluster.

⚡ **Pro Tip**: LoadBalancer services are typically used in cloud environments where the cloud provider can provision an external load balancer. They expose your services externally for external traffic.

