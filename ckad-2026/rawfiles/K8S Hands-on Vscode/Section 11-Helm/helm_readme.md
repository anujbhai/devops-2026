
# Kubernetes Hands-on: Using Helm for CKAD Exam Preparation

In this hands-on session, we will learn how to use **Helm** to package, deploy, and manage Kubernetes applications. Helm is a powerful package manager that simplifies Kubernetes application management.  
We will explore the basics of Helm and how to use it effectively for CKAD exam preparation.

---

## 📌 1. Installing Helm

First, we need to install Helm. Helm can be installed on your local machine or on the control plane node in your Kubernetes cluster.

### Install Helm on MacOS using Homebrew:

```bash
brew install helm
```

### Install Helm on Linux:

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

### Verify Helm Installation:

```bash
helm version   # Should display the installed Helm version
```

---
## 📌 2. Add and Update a Helm Repository

Helm charts are stored in repositories.
Since the stable repository is deprecated, we’ll use the official ingress-nginx repo instead.

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

Verify Repositories
```bash
helm repo list
```

---

## 📌 3. Installing a Helm Chart

Once we have created a Helm chart, we can use it to install an application on our Kubernetes cluster.

### Example: Installing the Nginx Chart

```bash
helm install my-nginx ingress-nginx/ingress-nginx  # Install the Nginx ingress controller chart
```

Explanation:  
- `helm install` → Installs a Helm chart.
- `my-nginx` → Release name for the Helm chart.
- `ingress-nginx/ingress-nginx` → The name of the chart to install.

### Verify the Installation:

```bash
kubectl get pods    # Verify Nginx Ingress controller pods
```

---

## 📌 4. Upgrading a Helm Release

If you need to update an application installed with Helm, you can use the `helm upgrade` command.

### Example: Upgrading a Helm Release

```bash
helm upgrade my-nginx ingress-nginx/ingress-nginx --set controller.replicaCount=3  # Upgrade with new configuration
```

Explanation:  
- `helm upgrade` → Upgrades an existing release.
- `--set controller.replicaCount=3` → Overrides the default value for the replica count.

---

## 📌 5. Helm Repositories

Helm uses repositories to store and share charts. You can add a Helm repository to your local Helm client.

### Add a Helm Repository:

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx # Add the Helm charts repository
helm repo update                                     # Update your local Helm repository list
```

### Search for Charts:

```bash
helm search repo nginx                               # Search for Nginx charts in the added repositories
```

---

## 📌 6. Managing Helm Releases

Helm allows you to manage releases using `helm list`, `helm upgrade`, and `helm uninstall` commands.

### List Installed Helm Releases:

```bash
helm list   # List all installed Helm releases
```

### Uninstall a Helm Release:

```bash
helm uninstall my-nginx   # Uninstall the Helm release
```

### View Helm Release History:

```bash
helm history my-nginx   # View the release history for my-nginx
```

---

## 📌 7. Debugging and Troubleshooting Helm Releases

### View the Logs of a Helm Release:

```bash
helm status my-nginx   # View the status of the Helm release
kubectl logs <nginx-pod-name>   # View the logs of a specific pod
```

### Describe Resources Managed by Helm:

```bash
kubectl describe deployment my-nginx   # Describe the Nginx deployment created by Helm
```

### Debug Helm Templates:

```bash
helm template my-helm-chart   # Render the Kubernetes manifests without deploying
```

---

## ✅ Summary

- Installed and configured **Helm**.
- Created and deployed a **Helm Chart**.
- Managed **Helm releases** using `helm install`, `helm upgrade`, and `helm uninstall`.
- Added and used **Helm repositories** to find charts.
- Debugged and troubleshooted **Helm releases**.
- **Helm** simplifies the management of Kubernetes applications and is essential for the CKAD exam.

⚡ **Pro Tip**: Helm is useful for packaging complex applications and ensuring reproducibility in Kubernetes. Practice using Helm to prepare for the CKAD exam!

