# Kubernetes Hands-on: Creating a Blue-Green Deployment

In this hands-on session, we will create a **Blue-Green Deployment**, which allows us to run two versions of an application (blue = current, green = new) and switch traffic between them with zero downtime.  
We will use the declarative method and learn how to test, switch, and debug Blue-Green Deployments.

---

## 📌 1. Declarative Method (YAML)

We first create **two Deployments** (blue and green) and use a **Service** to control which version receives traffic.

 Example: `blue-green-deployment.yaml`

-->look at the yaml file in the folder

### Commands:

kubectl apply -f blue-green-deployment.yaml    # Create Deployments and Service



-----


### 📌 2. Debugging and Troubleshooting Blue-Green Deployments

kubectl get deployments -n default
Lists all Deployments in the default namespace.

kubectl get pods -l app=nginx -n default
Lists all Pods of both blue and green Deployments.


kubectl describe deployment nginx-blue
kubectl describe deployment nginx-green

Enable us to see more detailed information about each Deployment, including events and replica status like what we did before.

kubectl logs  pod-name
Displays logs from a Pod for debugging purposes using the specific pod name



### 📌 3. Switching Traffic (Blue → Green)
To shift traffic from blue to green, update the Service selector:


kubectl patch service nginx-service -p '{"spec":{"selector":{"app":"nginx","version":"green"}}}'

This reroutes all traffic to the green Pods.

If successful, you can later delete the blue Deployment.


Cleanup

kubectl delete deployment nginx-blue
kubectl delete deployment nginx-green
kubectl delete service nginx-service


✅ Summary
Created a Blue-Green Deployment with two Deployments (blue and green).

Used a Service selector to control traffic flow.

Switched traffic safely from blue → green.

Explored debugging with kubectl describe, kubectl logs, and label selectors.

Cleaned up with kubectl delete.