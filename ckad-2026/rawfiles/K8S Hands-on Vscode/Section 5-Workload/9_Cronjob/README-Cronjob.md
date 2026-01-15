# Kubernetes Hands-on: Running a CronJob

In this hands-on session, we will learn how to create and manage **CronJobs** in Kubernetes.  
A CronJob creates Jobs on a **time-based schedule**, much like a Linux cron job.  
This is useful for periodic tasks such as backups, reports, or cleanups.

---

## 📌 1. Declarative Method (YAML)

We first create a CronJob using a YAML file inside **VS Code**.

### Example: `cronjob.yaml`

```yaml
apiVersion: batch/v1
kind: CronJob          # We are creating a CronJob resource
metadata:
  name: nginx-cronjob           # Unique name of the CronJob
spec:
  schedule: "*/1 * * * *"      # Run every 1 minute
  jobTemplate:               # Template for the Job to run
    spec:
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx-container
            image: nginx:latest
            command: ["echo", "Hello CKAD CronJob!"]
          restartPolicy: Never
```

### Commands:

```bash
kubectl apply -f cronjob.yaml     # Create the CronJob

---

## 📌 2. Imperative Method (Command Line)

We can also create a CronJob using the **imperative method**:

```bash
kubectl create cronjob nginx-cronjob \
  --schedule="*/1 * * * *" \
  --image=nginx:latest \
  --command -- echo "Hello CKAD CronJob!"

```

Explanation:  
- `kubectl create cronjob hello-cronjob` → Creates a CronJob named **nginx-cronjob**.  
- `--image=nginx:latest` → Uses the **nginx:latest** image.  
- `--schedule="*/1 * * * *"` → Runs every minute.  
- The rest (`"echo Hello CKAD CronJob!"`) is the command executed by the container.  

---

## 📌 3. Debugging and Troubleshooting CronJobs

### List CronJobs
```bash
kubectl get cronjobs
```

### Describe CronJob
```bash
kubectl describe cronjob nginx-cronjob
```
- Shows the schedule, last schedule time, and events.  

### Check Jobs Created by the CronJob
```bash
kubectl get jobs -n default
```

### View Logs from a Pod
```bash
kubectl logs <pod-name>
```

### Delete CronJob (also removes created Jobs)
```bash
kubectl delete cronjob nginx-cronjob
```

---

## ✅ Summary

- Created a CronJob **declaratively** using YAML.  
- Created the same CronJob **imperatively** with `kubectl create cronjob`.  
- Explored debugging tools:  
  - `kubectl get cronjobs`  
  - `kubectl describe cronjob`  
  - `kubectl get jobs`  
  - `kubectl logs`  
- Cleaned up with `kubectl delete cronjob`.  

⚡ **Pro Tip**: Use CronJobs for scheduled workloads. Test your `schedule` carefully — a wrong cron expression may run too often or not at all!
