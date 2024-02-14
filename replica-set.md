### Replica Set
In the past, keeping applications available meant using extra servers and, for some, having backup servers ready. 
This approach was expensive and slow, especially during failures, impacting businesses.

Kubernetes changes this with something called a Replica Set. A Replica Set ensures that a specified number of your application instances (pods) are always running. 
If any pods go down, it automatically creates new ones to maintain constant availability.

### Let's try to see the difference between the Pod definition file and replica set definition file
#### pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: myApp
    type: front-end
spec:
  containers:
  - name: my-app-container
    image: nginx
```
The above yaml configuration defines a single Pod named **my-app-pod** with one container running the nginx image. 
The labels app: myApp and type: front-end are used for identifying and organizing the Pod within the cluster.

#### replica-set.yaml
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-app-rs
  labels:
    app: myApp
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myApp
  template:
    metadata:
      labels:
        app: myApp
        type: front-end
    spec:
      containers:
      - name: my-app-container
        image: nginx!
```
The ReplicaSet definition aims to maintain **three replicas of a Pod running the nginx image**, as defined in its template. 
This template mirrors the metadata and spec sections of the Pod definition, ensuring that the ReplicaSet manages Pods that match its criteria.

### Key Differences and Why They Matter
- **Replica Count:** The replicas field in the ReplicaSet specifies that exactly three instances of the Pod should be running at all times, ensuring high availability of the application.
- **Template Section:** The template in the ReplicaSet is essentially a Pod definition that tells the ReplicaSet what the Pods it manages should look like. This includes the container image, name, and labels.
- **Selectors:** The selector field in the ReplicaSet uses the labels defined in the Pod's metadata to identify which Pods to manage. This mechanism ensures that only Pods with matching labels are considered part of the ReplicaSet.

### Below screenshot shows the ReplicaSet in action
![Screenshot 2024-02-15 at 12 24 18 AM](https://github.com/SalikeHassan/Kubernetes_Notes/assets/18566830/bb205464-9764-4ecb-9fab-b6f93ef83eb5)

Initially, a Pod is created from the pod.yaml file, resulting in a single instance named my-app-pod. This pod is running independently and is not managed by a ReplicaSet yet.

Upon applying the replica-set.yaml file, a ReplicaSet named my-app-rs is created, which is configured to ensure that there are always three replicas of the pod. 
Since there is already one pod with the matching labels (app: myApp), the ReplicaSet recognizes this pod as part of its desired state and only creates two additional pods to meet the specified replica count. 
Thus, you see a total of three pods, one from the initial pod definition and two new ones managed by the ReplicaSet.

![Screenshot 2024-02-15 at 12 25 05 AM](https://github.com/SalikeHassan/Kubernetes_Notes/assets/18566830/4da986e7-ad1c-42e9-905d-d098988b4632)
After the initial state where three pods are running, one of the pods, my-app-pod, is manually deleted. 
The ReplicaSet observes this change and detects that the number of running pods is now below the desired count of three. 
In response, the ReplicaSet takes action to rectify this by creating a new pod to replace the deleted one. 
The kubectl get pods command shows that a new pod is in the process of being created, indicated by the ContainerCreating status. 
This demonstrates the self-healing feature of the ReplicaSet, which ensures that the specified number of replicas are always maintained, 
thus providing reliability and high availability.

### Commands
- Create ReplicaSet from a file
  ```
  kubectl apply -f file-name.yaml
  ```
- Viewing ReplicaSet <br>
  ```
  kubectl get replicaset
   ```
- Get a list of ReplicaSet across all namespaces <br>
  ```
  kubectl get replicaset --all-namespaces
  ```
- Describe a specific ReplicaSet <br>
  ```
  kubectl describe replicaset <replicaset-name>
  ```
- Scaling ReplicaSet <br>
  ```
  kubectl scale replicaset <replicaset-name> --replicas=<count>
  ```
  **Note:** It always best practice to update the desired number of replicaset in definition file. Post update in file, run the kubectl apply command.
- Deleting ReplicaSet <br>
  ```
  kubectl delete replicaset <replicaset-name>
  ```
