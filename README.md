# Kubernetes Notes

Welcome to my Kubernetes Notes repository! Here, you will find comprehensive notes and insights into Kubernetes.

## High-Level Architecture of Kubernetes

For a foundational understanding of Kubernetes' high-level architecture, I recommend reading my blog post:

[**Kubernetes Architecture: A Beginner's Guide**](https://medium.com/@salikehassan93/kubernetes-architecture-a-beginners-guide-to-master-and-worker-nodes-c316512c9dc)

## Introduction to Kubernetes

Kubernetes orchestrates containerized applications by automating their deployment, scaling, and management. It operates across a cluster of nodes, serving as a central control plane. This ensures that your distributed applications run in the state you define.</br> 
The term "orchestration" in the context of Kubernetes refers to its capability to manage containerized applications within a cluster. 
Kubernetes is responsible for several key functions:
- **Deployment:** Placing containers on the right nodes within the cluster.
- **Scaling:** Managing the number of replicas of each container to handle the workload.
- **Networking:** Allowing containers to communicate with each other and the outside world.
- **Self-Healing:** Detecting failures and automatically restarting or replacing containers.
- **Resource Management:** Optimizing the use of CPU, memory, and other resources.

## Kubernetes Resource Definitions

Every Kubernetes object, such as Pods, Deployments, and Services, must include the following fields:

1. **apiVersion:** Specifies the API version. `apiVersion: v1` for core objects and `apiVersion: apps/v1` for others like Deployments.
2. **kind:** The type of Kubernetes object being created (e.g., `Pod`, `Deployment`, `Service`).
3. **metadata:** Descriptive information for identifying and organizing objects. This includes names, labels, and annotations.
4. **spec:** The spec field specifies the desired state of the object. For a Pod, this field is essential, as it outlines critical configurations such as Containers, Volumes, Scheduling and Affinity, and Networking.

Example configuration for a Pod

```pod-definition.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```
