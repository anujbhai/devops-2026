# Kubernetes Hands-on: Running a Job

In this hands-on session, we will learn how to create and manage **Jobs** in Kubernetes.  
A Job creates one or more Pods and ensures that a specified task **runs to completion**.  
This is useful for batch processing or short-lived tasks.

---

## 📌 1. Declarative Method (YAML)

We first create a Job using a YAML file inside **VS Code**.

### Example: `job.yaml`

```yaml

apiVersion: batch/v1           # API version for Job
kind: Job                      # We are creating a Job resource
metadata:
  name: nginx-job               # Unique name of the Job
spec:
  completions: 1                # Number of successful completions
  parallelism: 1                # Number of Pods running in parallel
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
        command: ["echo", "Hello CKAD!"]  # Command the container runs
      restartPolicy: Never

```

### Commands:

```bash
kubectl apply -f job.yaml       # Create the Job

---

## 📌 2. Imperative Method (Command Line)

We can also create a Job using the **imperative method**:

```bash
kubectl create job nginx-job2 --image=nginx:latest -- echo Hello CKAD!


```

# Explanation:  

kubectl create job nginx-job2 → Creates a Job named nginx-job2.

--image=nginx:latest → Runs the Job using the nginx:latest container image.

-- /bin/sh -c "echo Hello CKAD!" → Overrides the container’s default command and runs echo Hello CKAD! inside a shell.

---

## 📌 3. Debugging and Troubleshooting Jobs

### List Jobs
```bash
kubectl get jobs
```

### Describe Job
```bash
kubectl describe job nginx-job
```
- Shows completion status, conditions, and events.  

### View Pod Logs
```bash
kubectl logs <nginx-job-pod-name>
```
- Displays output of the Job (digits of Pi).  

### Check Pods Created by the Job
```bash
kubectl get pods -l job-name=nginx-job
```

### Delete Job
```bash
kubectl delete job nginx-job   
```

---

## ✅ Summary

- Created a Job **declaratively** using YAML.  
- Created the same Job **imperatively** with `kubectl create job`.  
- Explored debugging tools:  
  - `kubectl get jobs`  
  - `kubectl describe job`  
  - `kubectl logs`  
- Cleaned up with `kubectl delete job`.  

⚡ **Pro Tip**: Jobs are often used for batch tasks. For recurring tasks, use **CronJobs**.
