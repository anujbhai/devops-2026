# Kubernetes Hands-on: Creating a Canary Deployment

In this hands-on session, we will create a **Canary Deployment**, which allows us to gradually roll out a new version of an application by running it alongside the stable version.  
We will explore the declarative method, then check how to debug and troubleshoot using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We first create a Canary Deployment using a YAML file inside **VS Code**.  
The idea is to run both the **stable version** and the **canary version** of the app at the same time.

### Example: `canary-deployment.yaml`

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-stable
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
      track: stable
  template:
    metadata:
      labels:
        app: nginx
        track: stable
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-canary
  labels:
    app: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
      track: canary
  template:
    metadata:
      labels:
        app: nginx
        track: canary
    spec:
      containers:
      - name: nginx
        image: nginx:1.22
        ports:
        - containerPort: 80
Key Fields Explained
nginx-stable Deployment → Runs 3 replicas of the stable version (nginx:1.21).

nginx-canary Deployment → Runs 1 replica of the new version (nginx:1.22).

labels track=stable / track=canary → Differentiate stable vs canary Pods.

Both Deployments share the same app=nginx label 

# service :
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

This Service will load-balance across all Pods with app=nginx (both stable and canary).


# Commands:

kubectl apply -f canary-deployment.yaml    # Create stable and canary Deployments and service




## 📌 2. Imperative Method (Command Line)

While Canary Deployments are usually declarative, you can simulate them imperatively by creating two separate Deployments:


kubectl create deployment nginx-stable --image=nginx:1.21 --replicas=3
kubectl create deployment nginx-canary --image=nginx:1.22 --replicas=1
kubectl get deployments
kubectl get pods
Explanation:

nginx-stable → Stable app version.

nginx-canary → Canary app version, limited replicas.

Together they form the Canary strategy.


### 📌 3. Debugging and Troubleshooting Canary Deployments

List Deployments & pods :

kubectl get deployments -n default
kubectl get pods 

'Describe Deployment

kubectl describe deployment nginx-canary
'Shows canary Pod details, rollout progress, and events.

List Pods by Track:

kubectl get pods -l app=nginx,track=canary -n default
kubectl get pods -l app=nginx,track=stable -n default
Separates stable vs canary Pods.

View Logs






























































kubectl logs <canary-pod-name>
kubectl logs <stable-pod-name>
Compare logs between versions.

Rollout Status

kubectl rollout status deployment/nginx-canary
Check if the canary is running correctly.

#### 📌  4.Adjust Replicas to Control Traffic

# Initial rollout:

Stable: 3 replicas
Canary: 1 replica
→ ~25% of traffic goes to the Canary.



# Increase Canary traffic to 50%:

kubectl scale deployment nginx-canary --replicas=2
kubectl scale deployment nginx-stable --replicas=2


# Full switch (100% Canary, 0% Stable):

kubectl scale deployment nginx-canary --replicas=4
kubectl scale deployment nginx-stable --replicas=0



# Delete old stable deployment : 

kubectl delete deployment nginx-stable

Remove the canary Deployment if needed.



✅ This is the basic Kubernetes way of gradually shifting traffic — by manipulating replica counts.
⚡ If you need true percentage-based traffic routing (e.g., 10%, 20%, 50%), you’d use service mesh tools like Istio, Linkerd, or Argo Rollouts, which allow weighted routing without relying only on Pod counts.

⚡ **Pro Tip**: For the CKAD exam,
Be quick with kubectl get, kubectl scale, and kubectl rollout status to monitor progress.

Always label Pods clearly (track=stable, track=canary) so you can filter and debug them efficiently.

👉 Mastering this workflow will help you confidently handle Canary rollouts during the exam.