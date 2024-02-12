### Introduction
One way to create a pod in a Kubernetes node is by running an imperative command ```kubectl run <pod-name> --image=nginx ```, which automatically creates the pod for us. 
However, there are many steps that occur in the background to facilitate the creation of a pod.

For the sake of simplicity, let's focus on the very basic pod lifecycle and its phases. 
In more advanced topics, I will cover the more complex aspects of the pod lifecycle. There, we will discuss Pod Liveness, Readiness, and 
Pod Hooks and init containers, among others.

### Pod Lifecycle
A pod follows a lifecylce, let's look at them.
1. **Pending:** Pod has been created, but awaits node assignment and container setup.
2. **Running:** Pod is bound to a node; at least one container is running successfully.
3. **Succeeded:** All containers have exited with success (code 0).
4. **Failed:** At least one container has exited with a failure (non-zero exit code).

By running command ``` kubectl describe pod <pod-name> ``` we can see the lifecycle events of a pod.

#### Let's try to understand it with the help of flow
![pod-lifecycle](https://github.com/SalikeHassan/Kubernetes_Notes/assets/18566830/f418ea44-3222-4f47-a48b-9b2896500dde)

When we run the command to create a Pod, the following steps are performed:

**Step 1:** The **API Server** authenticates and authorizes the user. Upon successful authentication and authorization, the request is stored in `etcd`. At this point, the Pod enters the **Pending** state.

**Step 2:** The **Scheduler** begins searching for a suitable node based on resource availability. (More factors on which the scheduler selects a node, such as node selector, node affinity, taints, and tolerations, will be covered in a separate topic). Once it identifies an appropriate node, it fills in the `label spec` with the node name and sends this information back to the API Server. Subsequently, this request is saved in `etcd`.

**Step 3:** The **API Server** instructs the kubelet on the designated node that there is a Pod that needs to be spawned. It is the kubelet's responsibility to pull the image from a cloud repository like DockerHub if it is not already available on the node.

**Step 4:** Once the image is pulled and the container runtime is ready, the Pod's state is updated to the **Running** state.

### POD Behaviour
Pods are ephemeral, non self healing and non-presistent entities. 
They do not have the capability to self-heal. This means:

- **Node Failure:** If a Pod is scheduled on a node that later fails, the Pod will be lost. Kubernetes does not automatically reschedule or restart the failed Pod on a new node.
- **Eviction:** Pods can be evicted from a node for several reasons, such as resource scarcity (CPU, memory) or node maintenance. When a Pod is evicted, it is deleted and not automatically rescheduled.
- **Lack of Persistence:** If a Pod dies, any data stored in it is lost unless that data is stored on a persistent storage solution outside of the Pod.

### Controllers

To manage the lifecycle of Pods and ensure the desired state of applications, Kubernetes introduces the concept of controllers. 
Controllers are higher-level abstractions that monitor the state of Pods and take actions to reconcile the current state with the desired state. 

Some common controllers include:
- **ReplicaSet:** Ensures that a specified number of Pod replicas are running at any given time. If a Pod fails, the ReplicaSet will start new Pods to maintain the desired count.
- **Deployment:** Manages the deployment and scaling of a set of Pods and provides updates to the Pods along with their configurations. Deployments use ReplicaSets under the hood.
- **StatefulSet:** Similar to Deployments, but for managing stateful applications. It provides unique, persistent identities for each Pod and can manage the deployment and scaling of a set of Pods, as well as provide guarantees about the ordering and uniqueness of these Pods.
- **DaemonSet:** Ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected.





