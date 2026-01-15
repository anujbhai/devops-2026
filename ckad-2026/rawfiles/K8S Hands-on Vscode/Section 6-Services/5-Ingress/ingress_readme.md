# Kubernetes Hands-on: Ingress (Multiple Paths Example)

In this hands-on session, we will learn about **Ingress** in Kubernetes with path-based routing.
Ingress is an API object that manages external access to services in a cluster, typically HTTP or HTTPS.
It allows you to define routing rules to expose multiple applications under the same domain name but different paths — enabling advanced traffic management such as SSL termination, name-based virtual hosting, and path-based routing.

# We will:

Create two backend services (service-a and service-b).

Define an Ingress that routes:

example.com/a → service-a

example.com/b → service-b

Test and troubleshoot the Ingress.

## 📌 1. Declarative Method (YAML)

We will first create Ingress using a YAML file inside **VS Code**.

### Example: `Ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1        # API version for Ingress
kind: Ingress                           # Define Ingress resource
metadata:
  name: example-ingress                 # Name of the Ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /   # Rewrite path before forwarding
spec:
  ingressClassName: nginx               # Specify NGINX Ingress controller
  rules:
  - host: example.com                   # Domain name handled by this Ingress
    http:
      paths:
      - path: /a                        # Requests to example.com/a
        pathType: Prefix
        backend:
          service:
            name: service-a             # Forward to service-a
            port:
              number: 80
      - path: /a                       # Requests to example.com/a
        pathType: Prefix
        backend:
          service:
            name: service-b             # Forward to service-b
            port:
              number: 80


### Key Fields Explained

apiVersion: networking.k8s.io/v1 → Specifies the API version for Ingress.
kind: Ingress → Declares we are creating an Ingress resource.
metadata.name → Names the Ingress example-ingress.
annotations → Extra configuration for the NGINX controller; rewrite-target: / strips the prefix.
spec.ingressClassName: nginx → Tells Kubernetes which controller to use.
rules → Defines the routing rules.
host: example.com → Specifies the host (domain name).
Each path entry matches a route prefix:

/a → forwards traffic to service-a
/b → forwards traffic to service-b

backend.service.name and port.number → Define which internal service and port receive the traffic.



### Commands:

```bash
kubectl apply -f Ingress.yaml    # Create the Ingress,it create also the deployment and services to test ingress
```

 Create the deployment and service necessary to make the ingress test :

 ```bash
kubectl apply -f deployment-service.yaml 
```



## 📌 2. Debugging and Troubleshooting Services

### List ingress :
```bash
kubectl get ingress

➡ Lists all ingress resources with their hosts, rules, and addresses.
```
### Describe Ingress :
```bash
kubectl describe ingress example-ingress


➡ Displays detailed information, backend mappings, and possible errors.
```

### By default, Minikube does not include the NGINX Ingress controller — you have to enable it manually.
Let’s go step by step 👇

🧩 Step 1 — Enable the Ingress addon:

```bash
minikube addons enable ingress
```

Then check if it’s running:

```bash
kubectl get pods -n kube-system
```

You should see something like:

nginx-ingress-controller-xxxxx   Running


## Test the routing


1- Edit the system hosts file :

** Open /etc/hosts with sudo
```bash
sudo nano /etc/hosts
```
You’ll be asked for your password (your user must have admin rights).

 ** Add the line at the end :
     127.0.0.1   example.com

2 -Forward a local port to the NGINX Ingress controller:
```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80
```
If 8080 is busy, use another free port like 8888.

Test Ingress Routing :

In you Browser tape:

http://example.com:8080/a
# Output: <h1>Hello from Service A</h1>

http://example.com:8080/b
# Output: <h1>Hello from Service B</h1>

Great ,it's working 


## ✅ Summary


This hands-on session demonstrated how to:

Expose multiple services using a single domain with path-based routing.

Create Ingress using both declarative YAML and imperative commands.

Test and troubleshoot routing behavior using common Kubernetes tools.

You can extend this example by adding TLS termination, rewrite rules, or multiple hosts in future exercises.