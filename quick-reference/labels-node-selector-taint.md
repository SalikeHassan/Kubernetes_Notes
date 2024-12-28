<img width="1041" alt="image" src="https://github.com/user-attachments/assets/9a46785d-332e-438d-b03c-67077e332092" />

<img width="1113" alt="image" src="https://github.com/user-attachments/assets/0ba4611c-565c-498d-bec9-be54de0b7cf6" />

<img width="1065" alt="image" src="https://github.com/user-attachments/assets/14fb89f4-faaf-4baa-95b5-c9559f926123" />

<img width="1171" alt="image" src="https://github.com/user-attachments/assets/668f7ffd-5bb7-4da8-af7c-ebf571dd8364" />

```bash
kubectl taint nodes blue-node color=blue:NoSchedule
kubectl taint nodes red-node color=red:NoSchedule
kubectl taint nodes green-node color=green:NoSchedule

kubectl label nodes blue-node color=blue
kubectl label nodes red-node color=red
kubectl label nodes green-node color=green
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: blue-pod
spec:
  tolerations:
  - key: "color"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
  containers:
  - name: app-container
    image: app-image
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: blue-pod
spec:
  tolerations:
  - key: "color"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: "color"
            operator: In
            values:
            - "blue"
  containers:
  - name: app-container
    image: app-image
```

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: <key>
            operator: <operator>
            values:
            - <value>
```
