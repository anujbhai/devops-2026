
# Kubernetes Hands-on: Using Field Selectors 

In this hands-on, we learn how to use Field Selectors to filter Kubernetes resources based on fields like status and metadata.

## 📌 1. Field Selectors with Pods

first create a pod :
```bash
kubectl run my-pod --image=nginx --restart=Never
```

```bash
kubectl get pods --field-selector=status.phase=Running       # List running Pods
kubectl get pods --field-selector=metadata.name=my-pod       # Select Pod by name
kubectl get pods --field-selector=status.phase!=Failed      # Exclude failed Pods
```
## 📌 2. Field Selectors with Nodes and Services
```bash
kubectl get nodes --field-selector=status.phase=Ready       # List ready Nodes
kubectl get services --field-selector=metadata.name=my-service  # Select Service by name
```
## 📌 3. Field Selectors with Jobs
```bash
kubectl get jobs --field-selector=status.succeeded=1        # List successful Jobs
kubectl get jobs --field-selector=metadata.name=my-job      # Select Job by name
```
## 📌 4. Debugging and Cleanup
```bash
kubectl describe pod my-pod
kubectl delete pod my-pod
```


✅ Summary

Use Field Selectors to filter resources by fields like status and metadata.

Works with Pods, Nodes, Services, and Jobs.

Combine with label selectors for more granular filtering.
