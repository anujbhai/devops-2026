
# CKAD Mock Exam - Practical Scenarios 
This mock exam is designed to simulate a real CKAD exam with hands-on tasks. Try to solve each question in your cluster before checking the solution.

---

## ⚡ Instructions
- You have **120 minutes** to complete 20 tasks.  
- Use `kubectl` commands and YAML manifests.  
- Each question tests a different CKAD domain: Pods, Deployments, ConfigMaps, Secrets, Scheduling, Networking, Volumes, etc.
---

## 🧠 Questions & Solutions


### 1. Create a Pod

Task: Create a pod named httpd-pod using the httpd:2.4 image.
#### Solution:


kubectl run httpd-pod --image=httpd:2.4 --restart=Never


Or YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - name: httpd
    image: httpd:2.4
```
### 3. Expose a Deployment

Task: Expose api-deploy on port 5000 as a NodePort service.

#### Solution:
```bash
kubectl expose deployment api-deploy --port=5000 --type=NodePort
```
### 4. ConfigMap Volume

Task: Create a ConfigMap app-settings with APP_MODE=production and mount it in /etc/config in a pod config-pod.

#### Solution:
```bash
kubectl create configmap app-settings --from-literal=APP_MODE=production
```

Pod YAML:
``` yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-settings
```
### 5. Secret File Mount

Task: Create a generic secret tls-secret with tls.crt and tls.key files, mount them in /etc/tls in a pod tls-pod.

#### Solution:
```bash
kubectl create secret generic tls-secret --from-file=tls.crt --from-file=tls.key
```

Pod YAML snippet:
```yaml
volumeMounts:
- name: tls-volume
  mountPath: /etc/tls
volumes:
- name: tls-volume
  secret:
    secretName: tls-secret
```
### 6. Resource Requests

Task: Create a pod mem-pod requesting 200m CPU and 256Mi memory.

#### Solution:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mem-pod
spec:
  containers:
  - name: app
    image: busybox
    resources:
      requests:
        cpu: "200m"
        memory: "256Mi"
    command: ["sleep","3600"]
```
### 7. Node Selector

Task: Schedule a pod gpu-pod only on nodes with label gpu=true.

#### Solution:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
  - name: gpu-app
    image: nvidia/cuda:12.2
  nodeSelector:
    gpu: "true"
```
### 8. Tolerations

Task: Allow pod special-pod to run on a node tainted special=true:NoSchedule.

#### Solution:
```yaml
tolerations:
- key: "special"
  operator: "Equal"
  value: "true"
  effect: "NoSchedule"
```
### 9. Liveness & Startup Probe

Task: Add a startup probe on /startup and liveness probe on /live.

#### Solution:
```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 5
  periodSeconds: 5
livenessProbe:
  httpGet:
    path: /live
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```
### 10. HostPath Volume

Task: Create a pod with hostPath volume mounted at /data in container.

#### Solution:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hostpath-pod
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep","3600"]
    volumeMounts:
    - name: host-volume
      mountPath: /data
  volumes:
  - name: host-volume
    hostPath:
      path: /tmp
```
### 11. StatefulSet with PVC

Task: Deploy a StatefulSet db with 2 replicas, each having 2Gi storage.

#### Solution:
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: db
spec:
  serviceName: "db"
  replicas: 2
  selector:
    matchLabels:
      app: db
  template:
    metadata:
      labels:
        app: db
    spec:
      containers:
      - name: postgres
        image: postgres:15
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 2Gi
```
### 12. DaemonSet with Labels

Task: Deploy DaemonSet metrics-agent on all nodes labeled monitoring=true.

#### Solution:
``` yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: metrics-agent
spec:
  selector:
    matchLabels:
      app: metrics-agent
  template:
    metadata:
      labels:
        app: metrics-agent
    spec:
      containers:
      - name: agent
        image: busybox
        command: ["sleep","3600"]
  nodeSelector:
    monitoring: "true"
```
### 13. CronJob with Args

Task: Run a job every 2 hours printing “Backup Running”.

#### Solution:
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-job
spec:
  schedule: "0 */2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: busybox
            args: ["echo", "Backup Running"]
          restartPolicy: OnFailure
```
### 14. NetworkPolicy with Egress

Task: Allow pod web to send traffic only to pods with role=db.

#### Solution:

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-db-egress
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          role: db
```
15. Helm Upgrade

Task: Upgrade release my-nginx to bitnami/nginx version 15.2.0.

#### Solution:
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm upgrade my-nginx bitnami/nginx --version 15.2.0
```
### 16. Update Deployment Environment Variable

Task: Add env LOG_LEVEL=debug to deployment api-deploy.

#### Solution:
```bash
kubectl set env deployment/api-deploy LOG_LEVEL=debug
```
17. Rollout Status Check

Task: Check rollout status of deployment api-deploy.

#### Solution:
```bash
kubectl rollout status deployment/api-deploy
```
### 18. Scale StatefulSet

Task: Scale StatefulSet db to 3 replicas.

#### Solution:
```bash
kubectl scale statefulset db --replicas=3
```
### 19. Access Pod Logs with Container Name

Task: View logs of container api in pod api-deploy-0.

#### Solution:
```bash
kubectl logs api-deploy-0 -c api
```
### 20. Exec into Pod with TTY

Task: Execute a bash shell in pod httpd-pod.

#### Solution:
```bash
kubectl exec -it httpd-pod -- /bin/bash
```


# ✅ End of Mock Exam

This set covers pods, deployments, services, ConfigMaps, Secrets, volumes, scheduling, probes, network policies, Helm, and batch jobs—perfect for hands-on CKAD practice.