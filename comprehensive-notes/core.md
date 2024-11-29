# Kubernetes: Nodes, Clusters, and Key Concepts

## Nodes and Clusters

### Node
- A physical or virtual machine where Kubernetes is installed (worker node).
- **Purpose**: Responsible for running containers.

### Cluster
- A group of nodes working together.
- **Purpose**: Ensures application availability and load sharing, even if a node fails.

---

### Master Node
- **Purpose**: Manages the cluster and orchestrates container deployments.
- **Key Components**:
  - **API Server**: Frontend for the Kubernetes cluster.
  - **etcd**: Distributed key-value store for cluster data.
  - **Scheduler**: Assigns containers to nodes.
  - **Controller Manager**: Ensures the cluster state matches the desired state.
  - **Container Runtime**: Runs containers (e.g., Docker, containerd).
  - **kubelet**: Agent on each worker node ensuring container health and responding to the master.

---

## Kubernetes CLI Tool: `kubectl`
- **Purpose**: Deploy and manage applications, inspect nodes, and fetch cluster details.
- **Key Commands**:
  - `kubectl run`: Deploy an application.
  - `kubectl cluster-info`: View cluster information.
  - `kubectl get nodes`: List nodes in the cluster.

---

## Container Runtime Evolution

### Docker
- Initially the dominant container runtime.
- Features: Image building, networking, and container runtime (`runC`).

### containerd
- Extracted from Docker as a standalone, OCI-compliant runtime.
- Compatible with Kubernetes via CRI.
- **Key CLI Tools**:
  - **ctr**: Debugging tool for `containerd` (limited features).
  - **nerdctl**: Docker-like CLI for `containerd` with advanced features (e.g., lazy image pulling).
  - **crictl**: Kubernetes CLI for debugging CRI-compatible runtimes, supports pod-level operations.

---

## Pods

### Pod
- **Definition**: Smallest deployable unit in Kubernetes.
- **Features**:
  - Encapsulates containers.
  - Provides shared network and storage.
  - Typically has a 1:1 relationship with containers but can host multiple related containers.

### Scaling
- **Purpose**: Adjust application load.
- Use new pods for scaling instead of adding containers to existing pods.
- **Key Commands**:
  - `kubectl run --image=<image>`: Deploy a container as a pod.
  - `kubectl get pods`: List pods in the cluster.

---

## Controllers

### Replication Controller (RC)
- Ensures a specified number of pods are running at all times.
- Supports high availability and load balancing.
- **Key Commands**:
  - `kubectl create -f <file>`: Create RC.
  - `kubectl get replicationcontroller`: View RCs.

### Replica Set (RS)
- Newer replacement for replication controllers with advanced label and selector mechanisms.
- **Differences**:
  - RS requires a selector to identify pods, while RC assumes labels match.
- **Key Commands**:
  - `kubectl create -f <file>`: Create RS.
  - `kubectl get replicasets`: View RSs.

---

## Deployments

### Deployment
- **Definition**: Higher-level abstraction for managing application lifecycles.
- **Capabilities**:
  - **Rolling Updates**: Incrementally update instances to avoid downtime.
  - **Rollback**: Undo recent changes.
  - **Pause/Resume**: Batch multiple changes before rolling out.

### Commands
- `kubectl create -f <file>`: Create a deployment.
- `kubectl get deployments`: View deployments.
- `kubectl get all`: List all cluster objects.

---

## Namespaces

### Purpose
- Isolate resources for different environments (e.g., dev, prod).
- Assign resource quotas to prevent over-utilization.

### Default Namespaces
- `default`: General-purpose.
- `kube-system`: Internal Kubernetes services.
- `kube-public`: Publicly accessible resources.

### DNS Naming
- Format: `service-name.namespace.svc.cluster.local`.

### Commands
- `kubectl get pods -n <namespace>`: List pods in a namespace.
- `kubectl create namespace <name>`: Create a namespace.
- `kubectl config set-context --current --namespace=<name>`: Switch namespace.

---

## Resource Management

### Scaling
- Update replicas via a definition file or `kubectl scale`.
- **Example**: `kubectl scale replicasets <name> --replicas=<number>`.

### Resource Quotas
- Limit resources in namespaces using a definition file.

---

## Component Commands Overview

| **Component**         | **Purpose**                           | **Commands**                              |
|------------------------|---------------------------------------|-------------------------------------------|
| **Pods**              | Encapsulate containers.              | `kubectl get pods`, `kubectl describe pod` |
| **Replication Ctrl**  | Ensure a fixed number of pods.        | `kubectl get replicationcontroller`       |
| **Replica Set**       | Advanced replication with selectors. | `kubectl get replicasets`                 |
| **Deployment**        | Manage application lifecycle.        | `kubectl get deployments`, `kubectl rollout` |
| **Namespace**         | Isolate resources.                   | `kubectl create namespace`, `kubectl config` |
