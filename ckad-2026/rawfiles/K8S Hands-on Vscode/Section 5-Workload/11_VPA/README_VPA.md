# Kubernetes Hands-on: Vertical Pod Autoscaler (VPA)

In this hands-on session, we will explore the **Vertical Pod Autoscaler (VPA)** in Kubernetes.  
Unlike the HPA, which adjusts the *number of replicas*, the VPA adjusts the **CPU and memory requests/limits** of Pods to ensure they run with the right amount of resources.

---

## 📌 1. Install VPA Components

The VPA requires three components:
- **Recommender** → Observes Pod resource usage and makes recommendations.
- **Updater** → Evicts Pods if needed to apply new recommendations.
- **Admission Controller** → Mutates Pods at creation to include recommended resources.

Install VPA component using the official manifest:

```bash
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler
./hack/vpa-up.sh

```

Verify installation:
```bash
kubectl get pods -n kube-system | grep vpa
```

---

## 📌 2. Create a Deployment

We will use a simple NGINX Deployment.

### Example: `deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 200m
            memory: 256Mi
        ports:
        - containerPort: 80
```

Apply it:
```bash
kubectl apply -f deployment.yaml
```

---

## 📌 3. Create a VPA Object

We now create a VPA to automatically adjust resource requests.

### Example: `vpa.yaml`
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: nginx-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind:       Deployment
    name:       nginx-deployment
  updatePolicy:
    updateMode: "Auto"
```

Apply it:
```bash
kubectl apply -f vpa.yaml
```

---

## 📌 4. Generate Load to Trigger Scaling

Run a BusyBox Pod to continuously send requests:

```bash
kubectl run -it --rm load-generator --image=busybox -- /bin/sh
```

Inside the Pod:
```sh
while true; do wget -q -O- http://nginx-deployment; done
```

To exit:
- Press `Ctrl + C` to stop the loop.  
- Type `exit` to leave the Pod. (It will be auto-deleted because of `--rm`)

---

## 📌 5. Check VPA Recommendations

```bash
kubectl describe vpa nginx-vpa
```

Look for the **Recommendation** section showing updated CPU/Memory requests.

---

## 📌 6. Clean Up

```bash
kubectl delete -f vpa.yaml
kubectl delete -f deployment.yaml
```

---

## ✅ Summary

- Installed **VPA components** (Recommender, Updater, Admission Controller).  
- Created a Deployment with CPU/Memory requests.  
- Applied a **VPA object** to adjust Pod resources.  
- Verified recommendations under load.  

⚡ **Pro Tip**: Use VPA together with HPA for full autoscaling (replicas + resources).

