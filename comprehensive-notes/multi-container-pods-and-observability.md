# Multi-Container Pods

## Definition
- A pod can contain **multiple containers** that share:
  - The same **lifecycle**.
  - The same **network** (single IP address).
  - The same **storage** (shared volumes).

## Purpose
- Facilitate **tightly coupled services**, ensuring they run together and communicate locally within the pod.

## Common Patterns

### 1. Sidecar
- **Use Case**: Logging agents.
  - Example: Collecting and forwarding logs from an application to a central logging system.

### 2. Adapter
- **Use Case**: Data transformation.
  - Example: Converting application outputs into standardized formats before sending to a server.

### 3. Ambassador
- **Use Case**: Proxying requests to external services.
  - Example: Connecting to different databases during various stages of development.

## Implementation
- Add multiple containers under the `spec.containers` array in the pod definition.


# Init Containers

## Definition
- Special containers that **run and complete** before the main containers in a pod.

## Purpose
- **Perform initialization tasks**, such as:
  - Fetching configuration files.
  - Waiting for dependent services to become available.
  - Setting up the environment for the main containers.

## Key Features
1. Declared under the `spec.initContainers` field in the pod definition.
2. **Execution**:
   - Run **sequentially** (one after another).
   - Must **complete successfully** for the pod to start its main containers.
  
```yaml
initContainers:
- name: init-task
  image: busybox
  command: ["sh", "-c", "echo 'Init container running' && sleep 5"]
containers:
- name: main-container
  image: nginx
```
# Probes

## Purpose
- Ensure the **health and readiness** of containers in a Kubernetes pod.

## Types of Probes

### 1. Readiness Probe
- **Purpose**: Determines if a container is ready to accept traffic.
- **Use Case**: Delays routing traffic to an application until it is fully initialized and ready to handle requests.

### 2. Liveness Probe
- **Purpose**: Ensures the container is alive and functioning properly.
- **Use Case**: Automatically restarts unresponsive or malfunctioning containers.

## Configuration Options
Probes can be configured using the following mechanisms:
1. **HTTP GET**:  
   - Sends an HTTP GET request to a specified endpoint.  
   - If the response code is successful (e.g., 200-399), the probe is considered successful.
```yml
readinessProbe:
  httpGet:
    path: "/healthz"
    port: 8080
```

2. **Command Execution**:  
   - Executes a specified command inside the container.  
   - If the command exits with a `0` status code, the probe is considered successful.
```yaml
livenessProbe:
  exec:
    command: ["cat", "/tmp/healthy"]
```

3. **TCP Socket**:  
   - Opens a TCP socket to the specified port.  
   - If the connection is successful, the probe is considered successful.
```yaml
livenessProbe:
  tcpSocket:
    port: 3306
```

## Example: Readiness and Liveness Probes
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-probes
spec:
  containers:
    - name: app-container
      image: my-app-image
      readinessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      livenessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 15
        periodSeconds: 20
```
# Logging

## Concept
- Logs are essential for **debugging** and **monitoring** applications running in Kubernetes.
- Provide insights into application behavior and issues.
---
# Monitoring

## Purpose
- Monitor **resource utilization** (e.g., CPU, memory) of nodes and pods in a Kubernetes cluster.

## Tools

### Metrics Server
- **Description**: A lightweight, built-in monitoring solution.
- **Features**:
  - Aggregates real-time resource usage data.
  - Does **not store historical metrics**.

## Commands

1. **View Node Metrics**:
   ```bash
    kubectl top node
    kubectl top pod
  ``

# Advanced Monitoring

## Advanced Monitoring Tools
- **Prometheus**: Open-source monitoring and alerting toolkit.
- **Elastic Stack**: Centralized logging and monitoring with Elasticsearch, Logstash, and Kibana.
- **Datadog**: Comprehensive monitoring for historical metrics, advanced analytics, and alerting.

## How Monitoring Works
1. **Kubelet**:
   - Each node runs a **Kubelet**, which collects metrics from containers using **cAdvisor** (Container Advisor).

2. **Metrics Server**:
   - Aggregates metrics data from all Kubelets.
   - Exposes the aggregated data via the **Kubernetes API**, enabling real-time access to resource metrics.

3. **Advanced Tools**:
   - Tools like Prometheus, Elastic Stack, or Datadog fetch metrics data from the Kubernetes API or integrate with the Metrics Server for historical and advanced analysis.
  
### Use Cases

## 1. Multi-Container Pods
- **Scenario**: Deploying tightly coupled services within a single pod.
  - **Example 1**: Deploy a web server alongside a logging agent to capture logs in real time.
  - **Example 2**: Use an ambassador container to proxy application requests to a database.

## 2. Init Containers
- **Scenario**: Performing initialization tasks before the main application starts.
  - **Example**: Ensure that the database is fully initialized and ready before starting the main application container.

## 3. Probes
- **Liveness Probe**:
  - **Use Case**: Restart a web server if it enters a deadlock to maintain availability.
- **Readiness Probe**:
  - **Use Case**: Delay routing traffic to a service until it completes its setup and is ready to handle requests.

## 4. Logging
- **Scenario**: Collecting logs for debugging and troubleshooting.
  - **Example**: Debug errors by retrieving logs from specific containers using:
    ```bash
    kubectl logs <pod-name> -c <container-name>
    ```

## 5. Monitoring
- **Scenario 1**: Real-time resource monitoring.
  - **Example**: Use `kubectl top node` to identify nodes with high CPU usage and scale the cluster accordingly.
- **Scenario 2**: Long-term performance analysis.
  - **Example**: Use **Prometheus** to analyze historical metrics and identify trends for optimizing resource usage.


