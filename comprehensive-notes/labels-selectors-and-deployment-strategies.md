# Labels, Selectors, and Deployment Strategies in Kubernetes
## Labels and Selectors

### What are Labels and Selectors?

#### Labels:
- **Definition**: Key-value pairs attached to Kubernetes objects.
- **Purpose**: Used for grouping and organizing objects.
- **Examples**:
  - `class=mammal`
  - `color=green`

#### Selectors:
- **Definition**: Used to filter and select Kubernetes objects based on their labels.
- **Examples**:
  - `class=mammal` returns all objects labeled as mammals.
  - `color=green` returns all green objects.

### Use Cases

1. **Grouping Objects**:
   - Group objects based on application, environment, or function.
   - Example: Label pods as `env=prod` or `app=nginx`.

2. **Object Discovery**:
   - **ReplicaSets** and **Services** use selectors to discover and manage pods.
   - Example: A Service routes traffic to pods labeled with `app=frontend`.

3. **Custom Filters**:
   - Filter Kubernetes objects based on specific labels.
   - Example:
     ```bash
     kubectl get pods --selector app=nginx
     ```
#### Example
```yaml
metadata:
  labels:
    app: app1
    tier: backend
kubectl get pods --selector app=app1
```
# Deployment Strategies

## Rollouts and Versioning
- **Rollouts**: Each deployment triggers a new rollout, creating a revision history.
- **Example**:
  - **Revision 1**: Initial deployment.
  - **Revision 2**: Triggered after an update, such as a new image version.

---

## Deployment Strategies

### 1. Recreate Strategy

- **Process**:
  - Shut down all old instances.
  - Deploy new instances of the updated application.
- **Drawback**:
  - Causes **downtime** during the update, as the old version is fully stopped before the new version starts.

---

### 2. Rolling Update Strategy (Default)

- **Process**:
  - Pods are updated **incrementally**, replacing old pods with new ones one at a time.
- **Advantage**:
  - Ensures **zero downtime** by keeping part of the application running during the update.

---

### 3. Blue-Green Deployment

- **Process**:
  1. Deploy the new version (green) **alongside** the old version (blue).
  2. Run **tests** on the new version to ensure stability.
  3. Switch all traffic to the new version **once tests are successful**.
- **Implementation**:
  - Use **different labels** for old and new versions (e.g., `version=v1` and `version=v2`).
  - Update the **service selector** to switch traffic from the old version to the new version.

### 4. Canary Deployment

#### Process:
- Deploy a **small percentage** of the new version alongside the old version.
- Gradually **increase traffic** to the new version while monitoring for issues.
- If successful, promote the new version; otherwise, roll back.

#### Implementation:
- **Replica Count Control**:
  - Reduce the replica count in the new deployment to control the amount of traffic routed to it.
  - Kubernetes distributes traffic evenly across all pods managed by a service.
- **Example**:
  - **Old Version**: 5 pods.
  - **New Version**: 1 pod (receives ~17% of traffic).
# Deployment Strategies Comparison

| **Strategy**     | **Use Case**                               | **Pros**                      | **Cons**                                   |
|-------------------|--------------------------------------------|--------------------------------|-------------------------------------------|
| **Recreate**      | Small applications where downtime is acceptable. | Simple.                      | Causes downtime.                          |
| **Rolling Update**| Zero-downtime updates.                    | Seamless upgrades.            | Slower updates.                           |
| **Blue-Green**    | Safe updates with quick rollback capability. | Traffic switch is simple.     | Requires double resources.                |
| **Canary**        | Gradual rollout for testing new versions.  | Fine-grained testing.         | Limited traffic control in native Kubernetes. |


### Examples
##### 1. Recreate Strategy
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: recreate-deployment
spec:
  replicas: 3
  strategy:
    type: Recreate
  selector:
    matchLabels:
      app: recreate-app
  template:
    metadata:
      labels:
        app: recreate-app
    spec:
      containers:
      - name: app-container
        image: nginx:1.16
```
##### 2. Rolling Update Strategy
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rolling-update-deployment
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: rolling-app
  template:
    metadata:
      labels:
        app: rolling-app
    spec:
      containers:
      - name: app-container
        image: nginx:1.16
```
##### 3. Blue-Green Strategy (Achieved through label changes)
###### Blue Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      version: v1
  template:
    metadata:
      labels:
        version: v1
    spec:
      containers:
      - name: app-container
        image: nginx:1.16
```
###### Green Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: green-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      version: v2
  template:
    metadata:
      labels:
        version: v2
    spec:
      containers:
      - name: app-container
        image: nginx:1.17
```
###### Switch the service selector to version: v2 for routing traffic to the green deployment.

##### 4. Canary Strategy
###### Primary Deployment
```yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: primary-deployment
spec:
  replicas: 5
  selector:
    matchLabels:
      app: front-end
  template:
    metadata:
      labels:
        app: front-end
        version: v1
    spec:
      containers:
      - name: app-container
        image: nginx:1.16
```
###### Canary Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: canary-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: front-end
  template:
    metadata:
      labels:
        app: front-end
        version: v2
    spec:
      containers:
      - name: app-container
        image: nginx:1.17
```

### Commands
```bash
Create deployment
kubectl apply -f deployment.yaml

Get deployments
kubectl get deployments
kubectl describe deployment <deployment-name>

Check Deployment Rollout Status
kubectl rollout status deployment <deployment-name>

View Pods Managed by a Deployment
kubectl get pods -l app=<label>

Update Deployment (e.g., Change Image)
kubectl set image deployment/<deployment-name> <container-name>=<new-image> --record

Scale Deployment
kubectl scale deployment <deployment-name> --replicas=<number-of-replicas>

Rollback to Previous Revision
kubectl rollout undo deployment <deployment-name>

Rollback to a Specific Revision
kubectl rollout undo deployment <deployment-name> --to-revision=<revision-number>

Check Deployment Rollout History
kubectl rollout history deployment <deployment-name>

Check Details of a Specific Revision
kubectl rollout history deployment <deployment-name> --revision=<revision-number>

Force Restart Deployment
kubectl rollout restart deployment <deployment-name>

View Events for Deployment
kubectl describe deployment <deployment-name>
```












