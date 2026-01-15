# Kubernetes Hands-on: Scaling a Deployment with HPA (Horizontal Pod Autoscaler)

In this hands-on session, we will create an **Nginx Deployment** and a **Horizontal Pod Autoscaler (HPA)** in Kubernetes.  
We will use both the **declarative method** (YAML files) and the **imperative method** (kubectl commands).  

---

## 📌 1. Declarative Method (YAML)

We will first create a Deployment and then attach an HPA to it using YAML files inside **VS Code**.

### Example: `deployment.yaml`

```yaml
apiVersion: apps/v1                  # API version for Deployment
kind: Deployment                     # Resource type: Deployment
metadata:
  name: nginx-deployment             # Name of the Deployment
  labels:
    app: nginx
spec:
  replicas: 1                        # Start with 1 Pod
  selector:
    matchLabels:
      app: nginx
  template:                          # Pod template definition
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx                  # Container name
        image: nginx:latest          # Nginx image
        resources:
          requests:
            cpu: 100m                # CPU request (important for HPA to work)
        ports:
        - containerPort: 80          # Expose port 80 inside the container
```

### Example: `hpa.yaml`

```yaml
apiVersion: autoscaling/v2           # API version for HPA
kind: HorizontalPodAutoscaler        # Resource type: HPA
metadata:
  name: nginx-hpa                    # Name of the HPA
spec:
  scaleTargetRef:                    # Target workload to scale
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment           # The Deployment name
  minReplicas: 1                     # Minimum Pods
  maxReplicas: 5                     # Maximum Pods
  metrics:                           # Metric used for scaling
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50       # Scale when average CPU > 50%
```

---

### Key Fields Explained

- **Deployment**: Runs a single replica of Nginx with CPU requests defined.  
- **HPA**: Monitors CPU utilization and adjusts replicas between **1 and 5**.  
- **averageUtilization: 50** → If CPU usage goes above 50%, the HPA will scale up.  

---

### Commands

```bash
kubectl apply -f deployment.yaml     # Create the Deployment
kubectl apply -f hpa.yaml            
```

---

## 📌 2. Imperative Method (Command Line)

We can also create the HPA directly using `kubectl` without writing YAML.

### Example:

```bash
kubectl create deployment nginx-deployment --image=nginx:latest
kubectl autoscale deployment nginx-deployment --cpu-percent=50 --min=1 --max=5
```

Explanation:  
- `kubectl create deployment` → Creates an Nginx Deployment.  
- `kubectl autoscale` → Attaches an HPA to it.  
- `--cpu-percent=50` → Set CPU utilization threshold.  
- `--min=1` / `--max=5` → Scaling range.  

---

## 📌 3. Debugging and Troubleshooting HPA

### List HPA
```bash
kubectl get hpa
```

### Describe HPA
```bash
kubectl describe hpa nginx-hpa
```
- Shows details about scaling activity, metrics, and events.  

### Check Pods
```bash
kubectl get pods -l app=nginx -n default
```
- Watch Pods scale up or down live.  

### Delete HPA
```bash
kubectl delete hpa nginx-hpa
```

### Delete Deployment
```bash
kubectl delete deployment nginx-deployment
```

---

## ✅ Summary

- Created an **Nginx Deployment**.  
- Configured a **Horizontal Pod Autoscaler (HPA)** to scale between **1–5 Pods** based on CPU usage.  
- Learned both **declarative (YAML)** and **imperative (kubectl)** methods.  
- Practiced debugging with `kubectl get`, `kubectl describe`, and Pod monitoring.  

⚡ **Pro Tip**: Always define **CPU requests** in your Deployment, otherwise the HPA will not have metrics to scale properly.  





































