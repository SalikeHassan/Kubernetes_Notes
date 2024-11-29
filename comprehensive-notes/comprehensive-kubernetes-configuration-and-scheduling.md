# Configuration Data Management

## ConfigMaps

### Purpose
- Store configuration data in key-value pairs.
- Inject configuration data into pods as environment variables or mount them as files.

---

### Creation Methods

#### 1. Imperative Creation
- **Command**: `kubectl create configmap <name> --from-literal=<key>=<value>`
  - Example: 
    ```bash
    kubectl create configmap app-config --from-literal=environment=production
    ```

- **Multiple Key-Value Pairs**:
  - Use `--from-literal` multiple times.
  - Example: 
    ```bash
    kubectl create configmap app-config --from-literal=env=production --from-literal=version=1.0
    ```

- **From a File**:
  - Use `--from-file` to create a ConfigMap from a file.
  - Example: 
    ```bash
    kubectl create configmap app-config --from-file=./config.txt
    ```
#### 2.	Declarative:
	-	Define in a YAML file:
 ```yaml
apiVersion: v1
kind: ConfigMap
```
# Secrets

## Purpose
- Store sensitive data such as passwords, tokens, or API keys.
- **Storage Format**: Stored in **Base64-encoded format**, but not encrypted by default.

### 1. Imperative Creation
- **Using Literal Key-Value Pairs**:
  ```bash
  kubectl create secret generic <name> --from-literal=<key>=<value>

metadata:
  name: <name>
data:
  <key>: <value>
```
```bash
kubectl create secret generic api-secret --from-literal=apiKey=12345abcde
```

#### 2.	Declarative:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: <name>
data:
  <key>: <base64-encoded-value>
```

# Resource Management: Requests and Limits

## Requests
- **Definition**: The guaranteed **minimum resources** allocated to a pod.
- **Purpose**: Ensures the pod has sufficient resources to operate.

## Limits
- **Definition**: The **maximum resources** a pod is allowed to use.
- **Purpose**: Prevents a single pod from consuming excessive resources, ensuring fair resource sharing.

```yaml
resources:
  requests:
    memory: "500Mi"
    cpu: "0.5"
  limits:
    memory: "1Gi"
    cpu: "1"
```

### Default Settings and Quotas
- Use LimitRange to set default resource requests and limits
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limits
spec:
  limits:
  - default:
      cpu: "1"
      memory: "1Gi"
    defaultRequest:
      cpu: "0.5"
      memory: "500Mi"
    max:
      cpu: "2"
      memory: "2Gi"
    min:
      cpu: "0.1"
      memory: "100Mi"
```
### Resource Quotas
- Restrict resource usage at the namespace level
  ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: compute-resources
    spec:
      hard:
        requests.cpu: "10"
        requests.memory: "20Gi"
        limits.cpu: "20"
        limits.memory: "40Gi"
  ```
---

# Scheduling
# Taints and Tolerations

## Taints

### Purpose
- Taints are applied to nodes to **restrict pod scheduling**.
- Prevent pods from being scheduled on a node unless they "tolerate" the taint.
- 

### Components of a Taint

1. **Key**: Identifier for the taint.
2. **Value**: Descriptive value for the taint.
3. **Effect**: Defines the taint's behavior.

### Effects

1. **NoSchedule**:  
   - Pods **without toleration** are **not scheduled** on the node.

2. **PreferNoSchedule**:  
   - The scheduler **avoids placing pods** on the node, but it is not strictly enforced.

3. **NoExecute**:  
   - Pods **without toleration** are:
     - **Evicted** if already running on the node.
     - **Unschedulable** if new pods attempt to run on the node.

---
# Security
## Security Contexts
- Define security settings at the pod or container level.

```yaml
securityContext:
  runAsUser: 1000
  capabilities:
    add: ["NET_ADMIN", "SYS_TIME"]
```
## Service Accounts

### Purpose
- **Service Accounts** are used by applications to authenticate with the Kubernetes API.

### Default Behavior
- Automatically created for each namespace.
- Mounted as a volume in pods unless `automountServiceAccountToken: false` is set in the pod specification.

### Creating a Service Account

### Command
```bash
kubectl create serviceaccount <name>
kubectl create serviceaccount my-service-account
```

# Feature Overview

| **Feature**          | **Purpose**                              | **Best Use**                          |
|-----------------------|------------------------------------------|---------------------------------------|
| **ConfigMaps**        | Store non-sensitive configuration data.  | Application settings, environment variables. |
| **Secrets**           | Store sensitive data in encoded format.  | Passwords, tokens, keys.              |
| **Resource Quotas**   | Limit resource usage per namespace.      | Multi-tenant clusters.                |
| **Taints/Tolerations**| Restrict pods from specific nodes.       | Node specialization, isolation.       |
| **Node Affinity**     | Control pod placement on nodes.          | Advanced placement logic.             |




