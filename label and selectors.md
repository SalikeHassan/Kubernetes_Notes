### Label
In Kubernetes, labels are a fundamental concept used to organize and select subsets of objects, such as pods, services, and deployments. 
They are key/value pairs attached to objects and can be used in various ways.

#### Label Structure
A label consists of a key and a value, both of which are strings. 
The key is divided into two parts: an optional prefix and a mandatory name, separated by a slash (/). 
This structure allows for namespacing and greater control over label keys.

- Name Segment
    - Mandatory part of the key.
    - Must be 63 characters or less.
    - Should begin and end with an alphanumeric character ([a-z0-9A-Z]).
    - Can include dashes (-), underscores (_), dots (.), and alphanumerics between.
- Prefix Segment
    - Optional part of the key.
    - Used for namespacing purposes.
    - Must be a DNS subdomain.
    - A DNS subdomain is composed of a series of DNS labels separated by dots (.).
    - Each label must start and end with an alphanumeric character with dashes allowed in between.
    - The entire prefix must not exceed 253 characters in length. Followed by a slash (/), if specified.

##### Pod definition with label
```
apiVersion: v1
kind: Pod
metadata:
  name: production-frontend-pod
  labels:
    environment: production
    tier: frontend
spec:
  containers:
  - name: frontend-container
    image: nginx
    ports:
    - containerPort: 80
```

##### Pod definition with Preifx label
```
apiVersion: v1
kind: Pod
metadata:
  name: team1-app-pod
  labels:
    team1.example.com/role: webapp
    team1.example.com/environment: production
spec:
  containers:
  - name: app-container
    image: nginx
    ports:
    - containerPort: 80
```



#### Let's try to understand the use case for Prefixes
Consider a scenario where multiple teams are working on a project and share a Kubernetes cluster. 
Implementing namespacing with prefixes helps prevent collisions between label keys and clarifies the scope or ownership of a label.

For instance, different teams might utilize their team domain as a prefix to distinguish their labels, such as team1.example.com/role/webapp versus team2.example.com/role/webapp. 
In the provided example, team1.example.com/role is the key, and webapp is the value, where team1.example.com is the prefix and role is the name segment.


### Selector
Label are not like Kubernetes names and UIDS, labels do not provide uniqueness.
Labels in kubernetes groups objects on their labels. Labels are designed to be shared by multiple objects, enabling efficient grouping and management. 
The core concept revolves around two types of **selectors: equality-based and set-based selectors**.

#### Equality-Based Selectors
Equality-based selectors allow you to select objects based on a specific label key and value. 
They use three operators for filtering: =, ==, and != .
*Kubernetes Service and ReplicationController supports Equality-Based Selectors*

**Let's try to understand with some examples**.
- environment = production or environment == production: This selects all objects that have a label with the key environment and the value production.
- tier != frontend: This selects all objects that either *do not* have the tier label or *have* a tier label with a value different from frontend.

Node can also have labels. If you want a pod to be deployed on a specifc Node you can do so with nodeSelector.
```
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "registry.k8s.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100

```
The above Pod definition ensures that the Pod will only be scheduled on nodes labeled with **accelerator=nvidia-tesla-p100**.

#### Set-Based Selectors
Set-based selectors provide a more flexible way to select objects based on the presence of a label key and/or value. 
They support three operations: in, notin, and exists.
*Kubernetes S Job, Deployment, ReplicaSet, and DaemonSet supports Equality-Based Selectors*

**Let's try to understand with some examples**.
- environment in (production, qa): Selects all objects with an environment label that has either the value production or qa.
- tier notin (frontend, backend): Selects all objects that either do not have a tier label or have a tier label with a value other than frontend or backend.
- partition (equivalent to partition exists): Selects all objects that have a label with the key partition, regardless of its value.
- !partition: Selects all objects that do not have a label with the key partition.

#### Combining Set-Based and Equality-Based selectors
For a complex scenario we can combine both selector. This can be achieved using AND operator. 
Comma acts as AND operator.
- partition in (customerA, customerB),environment!=qa: Selects objects labeled for customerA or customerB partitions and not in the qa environment.

#### MatchLabels and MatchExpression
So far we have discussed *Equality-Based Selectors* and *Set-Based Selectors*. Let's try to understand matchLabel and matchExpression.

- matchLabel: Straightforward way to select objects based on label keys and values.
  ```
  selector:
  matchLabels:
    component: redis
  ```
  Selects all objects that have a label with the key *component* and the value *redis*.
  
- matchExpressions: Sophisticated selection criteria using a list of conditions.
  Each condition specifies a key, an operator (In, NotIn, Exists, or DoesNotExist), and an optional array of values (for the In and NotIn operators).
  ```
  selector:
  matchExpressions:
    - { key: tier, operator: In, values: [cache] }
    - { key: environment, operator: NotIn, values: [dev] }
  ```
  Selects all objects with a *tier label* set to *cache* and either without an environment label or with an *environment label* set to anything other than *dev*.
##### Operators:
- In: Selects objects where the label's value matches any in the specified array.
- NotIn: Selects objects where the label's value does not match any in the specified array or the label does not exist.
- Exists: Selects objects that have a label with the specified key, regardless of its value.
- DoesNotExist: Selects objects that do not have a label with the specified key.

### Filtering Kubernetes objects based on Labels.
![Screenshot 2024-02-17 at 12 52 32 AM](https://github.com/SalikeHassan/Kubernetes_Notes/assets/18566830/4ba7f307-fa5e-4bc0-8dd4-3c33d6a95cef)
*List the Pods in the production environment and of the frontend tier.*

![Screenshot 2024-02-17 at 12 52 58 AM](https://github.com/SalikeHassan/Kubernetes_Notes/assets/18566830/37e97090-1ac1-4467-ae82-56c9622da044)
*List the Pods in either the production or QA environment.*


### Conclusion
Labels are a powerful feature in Kubernetes, offering a versatile way to organize, select, and manage resources. 
While it's possible to use a single label for Kubernetes objects, the true strength of labels is realized when multiple labels are applied.
