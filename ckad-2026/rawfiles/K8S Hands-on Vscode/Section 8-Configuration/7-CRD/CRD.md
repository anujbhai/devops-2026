# Kubernetes Hands-on: Using a CronTab CRD

In this session, we will create a CRD, add a custom resource, and inspect it in Minikube.CRDs allow you to extend Kubernetes with your own resource types.

## 📌 1. Create the CronTab CRD

Save this as crontab-crd.yaml:
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: crontabs.stable.example.com
spec:
  group: stable.example.com
  versions:
    - name: v1
      served: true
      storage: true
      schema:
        openAPIV3Schema:
          type: object
          properties:
            spec:
              type: object
              properties:
                cronSpec:
                  type: string
                image:
                  type: string
                replicas:
                  type: integer
  scope: Namespaced
  names:
    plural: crontabs
    singular: crontab
    kind: CronTab
    shortNames:
    - ct
```

Apply the CRD:

kubectl apply -f crontab-crd.yaml
kubectl get crd


You should see crontabs.stable.example.com listed.

## 📌 2. Create a CronTab Custom Resource

Save this as my-crontab.yaml:

```yaml
apiVersion: stable.example.com/v1
kind: CronTab
metadata:
  name: my-crontab
spec:
  cronSpec: "* * * * *"
  image: nginx:latest
  replicas: 2
```


Apply it:

kubectl apply -f my-crontab.yaml
kubectl get crontabs
kubectl describe crontab my-crontab


kubectl get crontabs → lists all CronTab resources.

kubectl describe crontab my-crontab → shows details, including cronSpec, image, and replicas.

# 📌 3. Inspecting the Custom Resource

Even though this CRD doesn’t create real Pods yet, you can practice accessing fields:

kubectl get crontab my-crontab -o yaml
kubectl get crontab my-crontab -o jsonpath='{.spec.cronSpec}'


These commands show the full YAML and specific fields.

This mimics how you would interact with any CRD in CKAD.

# ✅ Summary

Created a CRD (CronTab) in Minikube.

Created a custom resource (my-crontab) using the CRD.

Used kubectl get and kubectl describe to inspect the resource.

Learned how CRDs extend Kubernetes objects and how to interact with them.

⚡ **Pro Tip**:  For the CKAD exam, you may be asked to inspect or modify CRDs and custom resources. This example is a lightweight way to practice without needing an operator.