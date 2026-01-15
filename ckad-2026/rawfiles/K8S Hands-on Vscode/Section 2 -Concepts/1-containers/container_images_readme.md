
# Kubernetes Hands-on: Defining, Building, and Modifying Container Images

In this hands-on session, we will learn how to **define**, **build**, and **modify container images** for use in Kubernetes. 
We will explore how to create Docker container images, update them, and use them in Kubernetes Pods.

---

## 📌 1. Define Container Images

A **container image** is a lightweight, portable, and self-sufficient unit that contains everything needed to run a piece of software, such as the application code, libraries, dependencies, and environment settings. 
In Kubernetes, you can define which container images to use for your Pods.

### Example: Define a Container Image in a Pod

```yaml
apiVersion: v1                # The Kubernetes API version for Pods is "v1"
kind: Pod                     # We are creating a resource of type Pod
metadata:                      # Metadata contains identifying information
  name: my-nginx-pod           # The unique name of the Pod
spec:                         # The specification of the Pod
  containers:                  # List of containers inside this Pod
  - name: nginx-container     # Name of the container
    image: nginx:latest       # Define the container image (nginx, latest tag)
    ports:                     # Ports exposed by this container
    - containerPort: 80       # The application listens on port 80
```

### Key Fields Explained:
- **image** → Specifies the container image to use (e.g., `nginx:latest`).
- **name** → Defines the name of the container inside the Pod.

### Commands:

```bash
kubectl apply -f pod-with-image.yaml    # Create the Pod with the container image
kubectl get pods                       # Verify the Pod status
kubectl describe pod my-nginx-pod      # View Pod details
```

---

## 📌 2. Build Container Images

You can **build** container images using **Docker**. Docker allows you to create images from a set of instructions in a file called a **Dockerfile**.

### Example: `Dockerfile`

```dockerfile
# Use an official Nginx image as the base
FROM nginx:latest

# Copy the local content to the container
COPY ./index.html /usr/share/nginx/html/index.html

# Expose port 80 for the container
EXPOSE 80
```

### Steps to Build the Image:

1. **Create a Dockerfile**: Define the instructions on how to build your container image (as shown above).
2. **Build the Image**: Run the following command to build the Docker image from the Dockerfile.

```bash
docker build -t my-nginx-image .   # Build the image and tag it as "my-nginx-image"
```

3. **Verify the Image**: Check the list of available Docker images on your machine.

```bash
docker images   # List all images
```

### Push the Image to a Registry (Optional)

If you want to share the image, you can push it to a container registry (e.g., Docker Hub or Google Container Registry).

```bash
docker push my-nginx-image   # Push the image to a registry
```

---

## 📌 3. Modify Container Images

Sometimes, you need to **modify** a container image to add new features, update dependencies, or fix bugs. 
You can modify a container image by updating the **Dockerfile** and rebuilding the image.

### Example: Modify the `Dockerfile`

If you want to modify the content of your container, you can update the `Dockerfile`:

```dockerfile
# Use an official Nginx image as the base
FROM nginx:latest

# Modify the default index.html page to add custom content
COPY ./new_index.html /usr/share/nginx/html/index.html

# Expose port 80 for the container
EXPOSE 80
```

### Rebuild the Image

After modifying the `Dockerfile`, you can rebuild the container image:

```bash
docker build -t my-nginx-image .   # Rebuild the image
```

---

## 📌 4. Use the Modified Container Image in Kubernetes

Once the container image is built or modified, you can use it in your Kubernetes Pods.

### Example: Deploying Modified Container Image in Kubernetes

```yaml
apiVersion: v1                # The Kubernetes API version for Pods is "v1"
kind: Pod                     # We are creating a resource of type Pod
metadata:                      # Metadata contains identifying information
  name: my-modified-nginx-pod  # The unique name of the Pod
spec:                         # The specification of the Pod
  containers:                  # List of containers inside this Pod
  - name: nginx-container     # Name of the container
    image: my-nginx-image      # Use the modified container image (my-nginx-image)
    ports:                     # Ports exposed by this container
    - containerPort: 80       # The application listens on port 80
```

### Commands:

```bash
kubectl apply -f pod-with-modified-image.yaml    # Create the Pod with the modified container image
kubectl get pods                                  # Verify Pod status
kubectl describe pod my-modified-nginx-pod        # View Pod details
```

---

## 📌 5. Debugging and Troubleshooting Container Images

### View Container Logs

To view logs for the container in a Pod:

```bash
kubectl logs <pod-name>   # View logs for a specific container in a Pod
kubectl logs my-modified-nginx-pod   # View logs for the container in my-modified-nginx-pod
```

### Check Pod Status and Container Details

```bash
kubectl describe pod <pod-name>   # View detailed information about the Pod and container
```

### Rebuild and Redeploy Image

If you encounter issues with the container image, you can rebuild it and redeploy it to Kubernetes.

```bash
docker build -t my-nginx-image .   # Rebuild the image
kubectl set image pod/my-nginx-pod nginx-container=my-nginx-image  # Update the Pod with the new image
```

---

## ✅ Summary

- Defined, built, and modified **container images** using **Docker**.
- Used the modified container image in Kubernetes Pods.
- Key debugging tools:
  - `kubectl get pods`
  - `kubectl describe pod`
  - `kubectl logs <pod-name>`
  - `docker build -t <image-name> .`
  - `docker images`
  - `docker push <image-name>`
- Understanding how to create, modify, and manage container images is crucial for the CKAD exam.

⚡ **Pro Tip**: Practice creating and modifying container images, as this is a critical skill for deploying applications in Kubernetes. Building Docker images from scratch and updating them in response to changes is a valuable skill for Kubernetes management.

