
# Kubernetes Hands-on: Creating a Headless Service

In this hands-on session, we will create a **Headless Service** in Kubernetes.  
We will explore the declarative method (using a YAML file) and learn how to manage Headless Services using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We will first create a Headless Service using a YAML file inside **VS Code**.

### Example: `headless-service.yaml`

```yaml
apiVersion: v1                  # The Kubernetes API version for Services is "v1"
kind: Service                    # We are creating a resource of type Service
metadata:                        # Metadata contains identifying information
  name: my-headless-service      # The unique name of the Service
spec:                             # The specification of the Service
  selector:                       # Selector defines the Pods that this Service will route traffic to
    app: nginx                    # The label selector to find the Pods (e.g., "app=nginx")
  ports:                           # The ports exposed by this Service
    - protocol: TCP
      port: 80                     # Port exposed by the Service
      targetPort: 80               # Port on the Pod that the Service will forward to
  clusterIP: None                 # ClusterIP is set to "None" to make the Service headless
```

### Key Fields Explained
- **apiVersion: v1** → Kubernetes API version for Services.
- **kind: Service** → Defines the object type.
- **metadata** → Includes Service name (`my-headless-service`).
- **spec** → Defines desired state.
  - **selector** → Defines the label selector to find the Pods (e.g., `app: nginx`).
  - **ports** → Specifies the ports exposed by the Service (`80` here).
  - **clusterIP: None** → Makes the Service headless by setting `clusterIP` to `None`.

### Commands:

```bash
kubectl apply -f headless-service.yaml    # Create the Headless Service

### Create a pod to which service will send traffic :

kubectl run my-pod --image=nginx --labels="app=nginx"

```

## 📌 2. Debugging and Troubleshooting Services

### List Services
```bash
kubectl get svc -n default


You should see:

CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
None         <none>        80/TCP    1m


⚡ Notice that the ClusterIP is None


```

### Describe Service
```bash
kubectl describe service my-headless-service
```
- Shows details of the Service, including selector, ports, and associated endpoints.

### Get Endpoints :

kubectl get endpoints my-headless-service


### test the headless service :


Run a BusyBox Pod to test DNS lookup:

kubectl run test-pod --rm -it --image=busybox -- /bin/sh


Inside the shell:

nslookup my-headless-service


You’ll see multiple Pod IPs instead of a single ClusterIP — that’s the headless behavior.

* Access a Pod directly

Still inside the BusyBox Pod:

wget -qO- http://10.244.0.159:80


(or whichever Pod IP you saw from the endpoints).
This should return the NGINX default welcome page.


```bash


### Delete Service
```bash
kubectl delete service my-headless-service
```

### Delete Pods
```bash
kubectl delete pod my-pod
```
- Deleting Pods will cause the Service to route traffic to other Pods with matching labels.

---

## ✅ Summary

- Created a **Headless Service** **declaratively** using YAML.
- Explored how to manage Headless services with `kubectl`.
- Key debugging tools:
  - `kubectl get services`
  - `kubectl describe service`
  - `kubectl exec`
  - `kubectl delete`
- Headless services are used when you need direct access to Pods, such as for stateful applications or DNS-based service discovery.

⚡ **Pro Tip**: Headless services are useful for stateful workloads like databases, where you need to address Pods individually rather than via a single load-balanced IP.

