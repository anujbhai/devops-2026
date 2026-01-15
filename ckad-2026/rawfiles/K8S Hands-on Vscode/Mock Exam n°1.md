# CKAD Mock Exam - Practical Scenarios

This mock exam is designed to simulate a **real CKAD exam** with **hands-on tasks**. Try to solve each question in your cluster before checking the solution.

---

## ⚡ Instructions
- You have **120 minutes** to complete 20 tasks.  
- Use `kubectl` commands and YAML manifests.  
- Each question tests a different CKAD domain: Pods, Deployments, ConfigMaps, Secrets, Scheduling, Networking, Volumes, etc.  

---

## 🧠 Questions & Solutions


### 1. Create a Pod

**Task:** Create a pod named `nginx-pod` using the `nginx:1.23` image.

#### Solution:

```bash
kubectl run nginx-pod --image=nginx:1.23 --restart=Never
```
Or YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.23
```
### 2. Create a Deployment

**Task:** Create a deployment web-deploy with 3 replicas using nginx:1.23.

#### Solution:
```bash
kubectl create deployment web-deploy --image=nginx:1.23 --replicas=3
```
Or YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
```
### 3. Expose a Deployment

**Task:**  Expose web-deploy on port 80 as a ClusterIP service.

#### Solution:

```bash
kubectl expose deployment web-deploy --port=80 --type=ClusterIP
```
### 4. ConfigMap Environment Variable

**Task:** Create a ConfigMap app-config with key ENV=dev and mount it as an env var in a pod app-pod.

#### Solution:

```bash
kubectl create configmap app-config --from-literal=ENV=dev
Pod YAML:
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: nginx
    env:
    - name: ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: ENV
```
### 5. Secret Usage

**Task:** Create a generic secret db-secret with username=admin and password=pass123, and mount as env vars.

#### Solution:

```bash 
kubectl create secret generic db-secret --from-literal=username=admin --from-literal=password=pass123
```

Pod YAML snippet:

```yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username
- name: DB_PASS
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```
### 6. Resource Limits

**Task:**  Create a pod cpu-limited with 100m CPU limit and 200Mi memory limit.

#### Solution:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cpu-limited
spec:
  containers:
  - name: app
    image: nginx
    resources:
      limits:
        cpu: "100m"
        memory: "200Mi"
```
### 7. Node Affinity

**Task:**  Schedule a pod on nodes with label disktype=ssd.

#### Solution:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  containers:
  - name: app
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```
### 8. Tolerations

**Task:**  Allow pod to run on a node tainted with key=example:NoSchedule.

#### Solution:

```yaml
tolerations:
- key: "example"
  operator: "Exists"
  effect: "NoSchedule"
```
### 9. Readiness & Liveness Probe

**Task:** Add a readiness probe on /healthz and liveness probe on /alive.

#### Solution:

```yaml
readinessProbe:
  httpGet:
    path: /healthz
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 5
livenessProbe:
  httpGet:
    path: /alive
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 10
```
### 10. EmptyDir Volume

**Task:**  Create a pod with two containers sharing a temporary volume.

#### Solution:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-pod
spec:
  containers:
  - name: c1
    image: busybox
    command: ["sleep","3600"]
    volumeMounts:
    - name: shared-data
      mountPath: /data
  - name: c2
    image: busybox
    command: ["sleep","3600"]
    volumeMounts:
    - name: shared-data
      mountPath: /data
  volumes:
  - name: shared-data
    emptyDir: {}
```
### 11. StatefulSet

**Task:**  Deploy a StatefulSet web with 3 replicas and persistent storage.

#### Solution:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "web"
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 1Gi
```
### 12. DaemonSet

**Task:** Deploy a DaemonSet log-agent on all nodes.

#### Solution:

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-agent
spec:
  selector:
    matchLabels:
      name: log-agent
  template:
    metadata:
      labels:
        name: log-agent
    spec:
      containers:
      - name: agent
        image: busybox
        command: ["sleep","3600"]
```
### 13. CronJob

**Task:** Run a job every day at midnight.

#### Solution:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: daily-job
spec:
  schedule: "0 0 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            command: ["echo", "Hello"]
          restartPolicy: OnFailure
```
### 14. NetworkPolicy

**Task:** Allow ingress only from pods with label role=frontend.

#### Solution:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector: {}
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
```
### 15. Helm Deployment

**Task:** Install nginx chart with release name my-nginx.

#### Solution:

```bash

helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-nginx bitnami/nginx
```
### 16. Upgrade Deployment Image

**Task:** Update deployment web-deploy to nginx:1.25.

#### Solution:

```bash
kubectl set image deployment/web-deploy web=nginx:1.25
```
### 17. Rollback Deployment

**Task:** Rollback web-deploy to previous revision.

#### Solution:

```bash
kubectl rollout undo deployment/web-deploy
```
### 18. Scale Deployment

**Task:** Scale web-deploy to 5 replicas.

#### Solution:

```bash 
kubectl scale deployment web-deploy --replicas=5
```
### 19. Access Pod Logs

**Task:** View logs of pod nginx-pod.

#### Solution:

```bash 
kubectl logs nginx-pod
```
### 20. Exec into Pod

**Task:**  Execute a shell in pod nginx-pod.

#### Solution:

```bash
kubectl exec -it nginx-pod -- /bin/sh
```



# ✅ End of Mock Exam
This mock exam covers pods, deployments, services, ConfigMaps, Secrets, volumes, scheduling, probes, network policies, Helm, and batch jobs. Try to perform all tasks in a Minikube or KinD cluster for realistic practice.