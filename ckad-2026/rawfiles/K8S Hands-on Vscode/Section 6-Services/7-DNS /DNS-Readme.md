# 🧩 CKAD Hands-On Session: DNS for Pods and Services

In this hands-on session, we will learn how **DNS works in Kubernetes** and how to use it to connect between Pods and Services.  
Kubernetes provides built-in DNS resolution that lets Pods communicate using names instead of IPs.

We’ll cover both **Pod DNS** and **Service DNS**, showing how to test and verify them using `nslookup` and `wget`.

---

## 📌1. Declarative Method (YAML Files)

We will create:
- Two Pods (`nginx` and `busybox`)
- A Service (`nginx-svc`) exposing the `nginx` Pod on port 80.

### 📄 nginx-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```
```yaml
📄 nginx-svc.yaml

apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

📄 busybox.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["sleep", "3600"]

 ```   
Apply all files:
```bash
kubectl apply -f dns.yaml
```   

2. Testing DNS Inside the Cluster
🔹 Test Service DNS
Exec into the busybox pod:

```bash
kubectl exec -it busybox -- sh
clear
```  
Then run:
```bash
nslookup nginx-svc
```  
Expected output:

Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local
Name:      nginx-svc
Address 1: 10.102.45.89 nginx-svc.default.svc.cluster.local

✅ Explanation:

The DNS name nginx-svc.default.svc.cluster.local resolves to the ClusterIP of the Service.

This is the recommended way to connect applications inside Kubernetes.

Test HTTP access:

```bash
wget -qO- http://nginx-svc

``` 
You should get:

php-template
Copy code
<html>
<head><title>Welcome to nginx!</title></head>


...


## 📌1. Debugging and Verification
```bash
kubectl get svc
➡️ Lists all Services and their ClusterIPs.

kubectl describe svc nginx-svc
➡️ Shows endpoints and target Pods for the Service.

```
5. Cleanup
```bash
kubectl delete pod nginx busybox
kubectl delete svc nginx-svc
```
## ✅ Summary

In this hands-on session, we explored how Kubernetes DNS allows communication between Pods and Services.
We learned that Service DNS provides a stable, reliable way to access applications, while Pod DNS is mainly for debugging and testing.
Understanding both is essential for CKAD engineers to manage service discovery inside the cluster.

⚡ **Pro Tip**: DNS is an essential part of Kubernetes networking, and resolving services and Pods by DNS names is an efficient way to manage connections between microservices.
