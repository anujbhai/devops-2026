
# Kubernetes Hands-on: Creating Network Policies

In this hands-on session, we will create **Network Policies** in Kubernetes.  
We will explore the declarative method (using a YAML file) and learn how to manage Network Policies using `kubectl` commands.

---

## 📌 1. Declarative Method (YAML)

We will first create a Network Policy using a YAML file inside **VS Code**.

### Example: `network-policy.yaml`

```yaml
apiVersion: networking.k8s.io/v1   # API version for NetworkPolicy resources
kind: NetworkPolicy                # The resource type is NetworkPolicy
metadata:                    # Metadata section contains identifying information
  name: allow-nginx                # Name of the NetworkPolicy
spec:                              # Specification of the policy behavior
  podSelector:                     # Selects which pods this policy applies to
    matchLabels:                   # Uses labels to match specific pods
      app: nginx                   # Applies to pods with label app=nginx
  policyTypes:             # Defines the type of traffic this policy affects
  - Ingress                   # This policy controls incoming (Ingress) traffic
  ingress:                         # Defines rules for incoming traffic
  - from:                     # Specifies the allowed sources of incoming traffic
    - podSelector:                 # Select pods based on their labels
        matchLabels:               # Matching labels criteria for allowed pods
          access: allowed       # Only pods with label access=allowed can connect
    ports:                     # Specifies which ports and protocols are allowed
    - protocol: TCP                # Allow TCP protocol traffic
      port: 80                     # Allow traffic on port 80 (typically HTTP)

```


### Key Fields Explained
- **apiVersion: networking.k8s.io/v1** → Kubernetes API version for Network Policies.
- **kind: NetworkPolicy** → Defines the object type.
- **metadata** → Includes Network Policy name (`allow-nginx `).
- **spec** → Defines the desired state.
  - **podSelector** → Selects the Pods that the policy will apply to (e.g., `app: nginx`).
  - **policyTypes** → Defines whether the policy is for ingress or egress traffic (or both).
  - **ingress** → Defines rules for incoming traffic to Pods.
  
## 📌 2. Installing CNI  : Cilium 

Start a fresh Minikube cluster with Cilium as CNI
minikube start --cni=cilium


Minikube will automatically download and configure Cilium.

This cluster will now enforce NetworkPolicies correctly.

Verify Cilium is running
kubectl get pods -n kube-system


You should see pods like:

cilium-xxxxx         1/1 Running
cilium-operator-xxxxx 1/1 Running


If these are Running, Cilium is ready.




## 📌 3.Create Network Policy Commands:

```bash
kubectl apply -f NetWorkPolicy.yaml    # Create the Network Policy

```
then lets create the pod targeted by the networkpolicie :

# -------------------------
NGINX Pod
# -------------------------
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx                   # Label targeted by the NetworkPolicy
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
---



---
## 📌 4. Debugging and Troubleshooting Network Policies

### List Network Policies
```bash
kubectl get networkpolicies -n default
```

### Describe Network Policy
```bash
kubectl describe networkpolicy allow-nginx 
```
- Shows details of the Network Policy, including selected Pods, ingress, and egress rules.

## 📌 5. Test networkPolicy:

let's Create two pods : the first allowed pod which can access the nginx ,and the second denied pod that cannot access nginx

# -------------------------
# Allowed Pod (can access nginx)
# -------------------------
apiVersion: v1
kind: Pod
metadata:
  name: allowed-pod
  labels:
    access: allowed              # Matches the NetworkPolicy rule
spec:
  containers:
  - name: curl
    image: curlimages/curl
    command: ["sleep", "3600"]   # Keep running for testing
---
# -------------------------
# Denied Pod (cannot access nginx)
# -------------------------
apiVersion: v1
kind: Pod
metadata:
  name: denied-pod
spec:
  containers:
  - name: curl
    image: curlimages/curl
    command: ["sleep", "3600"]


### Test from allowed pod (✅ should work):

```bash
kubectl exec allowed-pod -- curl -s <Pod IP >
```
to get the pod IP use the command: 
```bash
kubectl get pod nginx -o wide
```
### Test from denied pod (🚫 should fail):

```bash
kubectl exec denied-pod -- curl -s <Pod IP >
```
you see it “stuck” is that the curl command is waiting for a connection or timing out.

Here’s how to handle it:

1️⃣ Cancel the command

Press Ctrl + C

This will immediately terminate the kubectl exec + curl session.

---

## ✅ Summary

- Created a **Network Policy** **declaratively** using YAML.
- Explored how to manage Network Policies with `kubectl`.
- Key debugging tools:
  - `kubectl get networkpolicies`
  - `kubectl describe networkpolicy`
  - `kubectl exec`
  - `kubectl delete`
- Network Policies control traffic between Pods, improving security by defining which Pods can communicate with each other.

⚡ **Pro Tip**: Use Network Policies to restrict traffic between Pods and minimize exposure in your applications.

