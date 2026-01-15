
# Kubernetes Hands-on: Installing Minikube on MacOS

In this hands-on session, we will learn how to install **Minikube** on MacOS. Minikube is a tool that allows you to run a local Kubernetes cluster on your machine for development and testing purposes.

---
## 📌 1. Install Docker first 

Minikube can also use Docker to manage the cluster. Install Docker if you prefer using it as the driver:


https://docs.docker.com/desktop/setup/install/mac-install/


After installing Docker, ensure it's running before starting Minikube.


## 📌 2. Install Minikube on MacOS

### Install Minikube Using Homebrew

If you have **Homebrew** installed, you can easily install Minikube with the following command:

```bash
brew install minikube   # Install Minikube using Homebrew
```

### Verify Installation

Once Minikube is installed, you can verify the installation by checking the Minikube version:

```bash
minikube version   # Should display the installed Minikube version
```

---

## 📌 3. Start Minikube

Once Minikube is installed, you can start a local Kubernetes cluster using the following command:

```bash
minikube start   # Start Minikube and initialize the local Kubernetes cluster
```

Minikube will automatically download the necessary Kubernetes components and start the local cluster.

### Verify Minikube Cluster Status

Once the cluster is started, you can verify the status of your Minikube cluster:

```bash
minikube status   # Verify the status of Minikube
```

This should display the status of the cluster, showing that Minikube is running.

### Access Kubernetes Dashboard

Minikube comes with a built-in Kubernetes dashboard. You can access it by running:

```bash
minikube dashboard   # Open the Kubernetes dashboard in your default browser
```

### Verify kubectl Access

Minikube sets up the `kubectl` configuration automatically. Verify that `kubectl` is configured to use the local Minikube cluster:

```bash
kubectl get nodes    # Verify that the node is running in Minikube
```

---

## 📌 4. Stop Minikube

When you're done working with Minikube, you can stop the cluster using:

```bash
minikube stop   # Stop the Minikube cluster
```

This will shut down the local Kubernetes cluster.

### Delete Minikube Cluster

If you no longer need the Minikube cluster, you can delete it to free up system resources:

```bash
minikube delete   # Delete the Minikube cluster
```

---

## 📌 5. Debugging and Troubleshooting Minikube

### View Minikube Logs

To view the logs of the Minikube VM, run:

```bash
minikube logs   # View the logs of the Minikube VM
```

### Reset Minikube

If you encounter any issues with your Minikube cluster, you can reset it:

```bash
minikube delete    # Delete and remove the current Minikube cluster
minikube start     # Start a fresh Minikube cluster
```

### Check Minikube Configuration

To check Minikube configuration details, run:

```bash
minikube config view   # View the current Minikube configuration
```

---

## ✅ Summary

- Installed **Minikube** on MacOS using **Homebrew**.
- Started a local Kubernetes cluster using Minikube.
- Accessed the Kubernetes dashboard and verified `kubectl` access.
- Stopped and deleted the Minikube cluster.
- Used Minikube for testing and development of Kubernetes workloads on a local machine.

⚡ **Pro Tip**: Minikube is a great tool for learning Kubernetes and testing workloads locally before deploying to a production cluster.

