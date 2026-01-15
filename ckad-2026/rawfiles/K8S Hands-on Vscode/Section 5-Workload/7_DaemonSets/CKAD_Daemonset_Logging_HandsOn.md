# CKAD Hands-on: DaemonSet with Logging Agent (Fluentd)

In this hands-on session, we will create a **DaemonSet** for a logging
agent (**Fluentd**).
A DaemonSet ensures that a copy of a Pod runs on all (or selected) nodes
in a Kubernetes cluster.
This is a common real-world use case, since logging agents must run on
every node to collect logs.

------------------------------------------------------------------------

## Step 📌 1: Create the DaemonSet YAML file

Create a file named **`fluentd-daemonset.yaml`** in VS Code and add the
following content:

``` yaml
apiVersion: apps/v1                   # API version for the DaemonSet resource
kind: DaemonSet                       # Defines this resource as a DaemonSet
metadata:
  name: fluentd-daemonset              # Name of the DaemonSet object
spec:
  selector:                           # Defines how to identify Pods managed by this DaemonSet
    matchLabels:
      app: fluentd                    # Match Pods with the label app=fluentd
  template:                           # Pod template used by the DaemonSet
    metadata:
      labels:
        app: fluentd                  # Label applied to Pods created by the DaemonSet
    spec:
      containers:                     # List of containers that will run inside each Pod
      - name: fluentd-container       # Name of the container
        image: fluent/fluentd:v1.16-debian-1  # Container image to use (Fluentd for log collection)
        volumeMounts:                 # Mount host volumes into the container
        - name: varlog                # Reference to the volume named "varlog"
          mountPath: /var/log         # Mounts the host's /var/log directory inside the container
      volumes:                        # Define volumes to be mounted in the Pod
      - name: varlog                  # Volume name
        hostPath:                     # Use a hostPath volume (maps directory from host to Pod)
          path: /var/log              # Path on the host machine that will be mounted

```

------------------------------------------------------------------------

## Step 📌 2: Apply the DaemonSet

Run the following command to create the DaemonSet:

``` bash
kubectl apply -f daemonset.yaml
```

------------------------------------------------------------------------

## Step 📌 3: Debugging and Troubleshooting

-   **List all DaemonSets in the default namespace:**

    kubectl get daemonsets -n default
   

-   **Describe the DaemonSet for details (Pods per node, events,template, etc.)**

    
    kubectl describe daemonset fluentd-daemonset
    

-   **List all Pods managed by this DaemonSet (label = app=fluentd):**

   
    kubectl get pods -l app=fluentd -n default
   

-   **View logs from a Pod for troubleshooting:**

 
    kubectl logs <fluentd-pod-name>
    

------------------------------------------------------------------------

## Step 📌  4: Cleanup

 kubectl delete daemonset fluentd-daemonset

delete the DaemonSet and its Pods:

------------------------------------------------------------------------

## ✅ Summary

-   We created a **DaemonSet for Fluentd**, a logging agent, using a
    declarative YAML file.
-   We applied it to the cluster and verified that Pods run on each
    node.
-   We explored different `kubectl` commands for listing, describing,
    and debugging the DaemonSet.
-   Finally, we cleaned up by deleting the resource.

👉 **Key takeaway:** DaemonSets are essential for running cluster-wide
services such as **logging, monitoring, or networking agents**.
