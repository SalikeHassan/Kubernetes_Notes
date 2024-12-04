<img width="1013" alt="image" src="https://github.com/user-attachments/assets/8e6c8abc-1994-4729-821b-46239e04f345">

<img width="1088" alt="image" src="https://github.com/user-attachments/assets/9f0edf5e-f6b4-4274-8936-631d830f4bfe">

<img width="1120" alt="image" src="https://github.com/user-attachments/assets/b29c349d-48db-4c74-aa22-30c53667c357">

```bash
	Node Label:
	•	The target node must have the label workload-type=worker.
	•	Apply this label to a node using:
kubectl label node <NODE_NAME> workload-type=worker
```
  
```yaml
Node Affinity Example
This ensures that the pod is scheduled only on nodes labeled with a specific key-value pair.

apiVersion: v1
kind: Pod
metadata:
  name: node-affinity-pod
  labels:
    app: affinity-app
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: workload-type
            operator: In
            values:
            - worker
  containers:
  - name: nginx
    image: nginx:latest
```

```yaml
Node Anti-Affinity Example
Ensures that replicas of the same app (or pods with the same label) avoid being scheduled on the same node.

apiVersion: apps/v1
kind: Deployment
metadata:
  name: anti-affinity-deployment
  labels:
    app: anti-affinity-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: anti-affinity-app
  template:
    metadata:
      labels:
        app: anti-affinity-app
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - anti-affinity-app
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: nginx:latest

Explanation:

	•	Pod Label:
	•	All replicas of the deployment are labeled app=anti-affinity-app.
	•	Anti-Affinity Rule:
	•	Pods with the label app=anti-affinity-app prefer not to run on the same node.
	•	The topologyKey of kubernetes.io/hostname ensures pods are scheduled on different nodes.
	•	Behavior:
	•	If there are 3 nodes available, each replica will be scheduled on a different node.
	•	If fewer nodes are available, Kubernetes will attempt to respect the anti-affinity but may relax it.
```
```yaml
This combines node affinity and pod anti-affinity for better scheduling.
apiVersion: apps/v1
kind: Deployment
metadata:
  name: affinity-combined-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: combined-app
  template:
    metadata:
      labels:
        app: combined-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: workload-type
                operator: In
                values:
                - worker
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - combined-app
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: nginx:latest

Explanation:

	1.	Node Affinity:
	•	Pods will only be scheduled on nodes with the label workload-type=worker.
	2.	Pod Anti-Affinity:
	•	Pods with the label app=combined-app will avoid being scheduled on the same node.
	3.	Behavior:
	•	Pods are spread across nodes labeled as workload-type=worker.
	•	They avoid being scheduled on the same node as other replicas.
```


