# CKAD Mock Exam- Advanced Practical Scenarios

This mock exam is designed to simulate a real CKAD exam with hands-on tasks. Try to solve each question in your cluster before checking the solution.

---

## ⚡ Instructions
- You have **120 minutes** to complete 20 tasks.  
- Use `kubectl` commands and YAML manifests.  
- Each question tests advanced CKAD topics: multi-container pods, init containers, advanced scheduling, persistent storage (CSI), complex networking, probes, and security.

## 🧠 Questions & Solutions
---

### 1. Multi-Container Pod

Task: Create a pod multi-pod with two containers: nginx (nginx:1.23) and sidecar (busybox, command: sleep 3600).

#### Solution:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.23
  - name: sidecar
    image: busybox
    command: ["sleep","3600"]

### 2. Init Container

Task: Create a pod init-pod with an init container that writes Hello into /data/message.txt before the main container runs.

#### Solution:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-pod
spec:
  volumes:
  - name: data-vol
    emptyDir: {}
  initContainers:
  - name: init
    image: busybox
    command: ["sh","-c","echo Hello > /data/message.txt"]
    volumeMounts:
    - name: data-vol
      mountPath: /data
  containers:
  - name: main
    image: busybox
    command: ["sleep","3600"]
    volumeMounts:
    - name: data-vol
      mountPath: /data
```
### 3. CSI Persistent Volume

Task: Create a PVC csi-pvc requesting 2Gi, mount it in pod csi-pod at /mnt/data.

#### Solution:

PVC:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: csi-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: csi-pod
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep","3600"]
    volumeMounts:
    - name: data
      mountPath: /mnt/data
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: csi-pvc
```
### 4. Pod Anti-Affinity

Task: Schedule pod anti-pod on a node without other pods labeled app=api.

#### Solution:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: anti-pod
spec:
  containers:
  - name: app
    image: nginx
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: api
        topologyKey: "kubernetes.io/hostname"
```
### 5. PriorityClass

Task: Create a high-priority pod priority-pod using a PriorityClass high-priority with value 1000.

#### Solution:

PriorityClass:
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000
globalDefault: false
description: "High priority pods"
```

Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: priority-pod
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep","3600"]
  priorityClassName: high-priority
```
### 6. Pod with HostNetwork

Task: Create pod hostnet-pod that uses the host’s network.

#### Solution:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostnet-pod
spec:
  hostNetwork: true
  containers:
  - name: app
    image: busybox
    command: ["sleep","3600"]

```
### 7. ConfigMap with Multiple Keys

Task: Create ConfigMap multi-config with KEY1=val1 and KEY2=val2, mount as env vars in config-env-pod.

#### Solution:
```bash
kubectl create configmap multi-config --from-literal=KEY1=val1 --from-literal=KEY2=val2
```

Pod YAML:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-env-pod
spec:
  containers:
  - name: app
    image: busybox
    env:
    - name: KEY1
      valueFrom:
        configMapKeyRef:
          name: multi-config
          key: KEY1
    - name: KEY2
      valueFrom:
        configMapKeyRef:
          name: multi-config
          key: KEY2
```
### 8. NetworkPolicy with Both Ingress & Egress

Task: Allow pod db to receive traffic only from pods with role=backend and send traffic only to role=cache.

#### Solution:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
  podSelector:
    matchLabels:
      app: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: backend
  egress:
  - to:
    - podSelector:
        matchLabels:
          role: cache
```
### 9. Sidecar Logging Container

Task: Add a logging sidecar to pod web that tails /var/log/app.log.

#### Solution:
```yaml
containers:
- name: web-app
  image: nginx
  volumeMounts:
  - name: logs
    mountPath: /var/log
- name: log-sidecar
  image: busybox
  command: ["sh","-c","tail -f /var/log/app.log"]
  volumeMounts:
  - name: logs
    mountPath: /var/log
volumes:
- name: logs
  emptyDir: {}
```
### 10. Rolling Update Strategy

Task: Create deployment rolling-deploy with 2 replicas using nginx and rolling update maxSurge=1, maxUnavailable=0.

#### Solution:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: rolling
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: rolling
    spec:
      containers:
      - name: nginx
        image: nginx:1.23
```
### 11. Pod Security Context

Task: Create pod secure-pod running as user 1000 and group 3000.

#### Solution:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
  containers:
  - name: app
    image: busybox
    command: ["sleep","3600"]
```
### 12. Deployment with Multiple Containers & Shared Volume

Task: Deployment shared-deploy with 2 containers sharing a PVC.

#### Solution:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: shared-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: shared
  template:
    metadata:
      labels:
        app: shared
    spec:
      containers:
      - name: main
        image: nginx
        volumeMounts:
        - name: shared-vol
          mountPath: /data
      - name: sidecar
        image: busybox
        command: ["sleep","3600"]
        volumeMounts:
        - name: shared-vol
          mountPath: /data
      volumes:
      - name: shared-vol
        persistentVolumeClaim:
          claimName: csi-pvc
```
### 13. CronJob with Environment Variable

Task: Create a CronJob env-job every hour with env ENV=staging.

#### Solution:
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: env-job
spec:
  schedule: "0 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: job
            image: busybox
            command: ["echo", "Running Job"]
            env:
            - name: ENV
              value: staging
          restartPolicy: OnFailure
```
### 14. StatefulSet with Headless Service

Task: StatefulSet cache with 3 replicas and headless service cache-svc.

#### Solution:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: cache-svc
spec:
  clusterIP: None
  selector:
    app: cache
ports:
- port: 6379
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: cache
spec:
  serviceName: "cache-svc"
  replicas: 3
  selector:
    matchLabels:
      app: cache
  template:
    metadata:
      labels:
        app: cache
    spec:
      containers:
      - name: redis
        image: redis:7
```
### 15. Horizontal Pod Autoscaler

Task: Create HPA for deployment api-deploy to scale between 2-5 replicas based on 50% CPU.

#### Solution:
```bash
kubectl autoscale deployment api-deploy --min=2 --max=5 --cpu-percent=50
```
### 16. Pod Disruption Budget

Task: Create PDB web-pdb allowing at most 1 pod down for deployment rolling-deploy.

#### Solution:
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: web-pdb
spec:
  maxUnavailable: 1
  selector:
    matchLabels:
      app: rolling
```
### 17. ServiceAccount & RBAC

Task: Create service account sa-app and bind it to view cluster role.

#### Solution:
```bash
kubectl create serviceaccount sa-app
kubectl create clusterrolebinding sa-app-view --clusterrole=view --serviceaccount=default:sa-app
```
### 18. Debug Container in Existing Pod

Task: Add a temporary debug container to pod api-deploy-0 using busybox.

#### Solution:
```bash
kubectl debug -it api-deploy-0 --image=busybox --target=api
```
### 19. Exec into Init Container

Task: Exec into init container init in pod init-pod.

#### Solution:
```bash
kubectl exec -it init-pod -c init -- /bin/sh
```
### 20. View Events and Describe Pod

Task: Describe pod priority-pod and view related events.

#### Solution:
```bash
kubectl describe pod priority-pod
kubectl get events --sort-by=.metadata.creationTimestamp
```

# ✅ End of Advanced Mock Exam

This advanced set covers multi-container pods, init containers, CSI volumes, pod anti-affinity, priority classes, sidecars, security contexts, HPA, PDB, NetworkPolicies, RBAC, and more—perfect for top-level CKAD practice.