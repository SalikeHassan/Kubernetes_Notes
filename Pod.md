### POD                                                                                               
- A pod is the basic and smallest deployable unit in Kubernetes.
- It encapsulates containers.
- Each pod has a unique identifier (UID) and is assigned to a specific node.
- Pods are ephemeral by design and are meant to be replaced if they fail.
- Kubernetes uses higher-level abstractions, called controllers, to manage Pods.

### Why is a Pod important in Kubernetes?
Scaling applications in Kubernetes can be achieved through various mechanisms. 
The most common method is horizontal scaling, which involves adding or removing pods based on the workload demand. 

### Containers and Pods
As defined above, a pod encapsulates containers. 
Although most of the time we observe a 1-1 relationship between a pod and a container, a pod can contain multiple containers.

### Create pod
- Using a pod definition file.
- Using an imperative command.
- Using a deployment.
- Using a replica set.

### Using a pod definition file
On Mac or Linux, create a file using the command ``` vim nginx-pod.yaml ```
```
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

Save and exit,then run the command
```
kubectl apply -f nginx-pod.yaml
```
**Note:** This command will only work if you are in the same file directory where you have created the pod definition file.

### Using imperative command
```
kubectl run nginx-pod2 --image=nginx
```
The above command will create nginx-pod2 pod

### To view the pods run the command
```
kubectl get pods
```
**Note: The other two approach of creating pod is part of the topic replica set and deployment**

### Commands
- Create pod from a file
  ```
  kubectl apply -f pod-definition.yaml
  ```
- Create pod using imperative command
  ```
  kubectl run <pod-name> --image=nginx
  ```
- Viewing Pods <br>
  ```
  kubectl get pods
   ```
- Get a list of Pods across all namespaces <br>
  ```
  kubectl get pods --all-namespaces
  ```
- Describe a specific Pod <br>
  ```
  kubectl describe pod <pod-name>
  ```
- Editing Pods <br>
  ```
   kubectl edit pod <pod-name> 
  ```
- Deleting Pods <br>
  ```
  kubectl delete pod <pod-name>
  ```
- Delete Pods using label selectors <br>
  ```
  kubectl delete -f pod-definition.yaml
  ```
