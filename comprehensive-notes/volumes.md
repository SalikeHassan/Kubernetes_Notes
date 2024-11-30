
<img width="1004" alt="image" src="https://github.com/user-attachments/assets/e9adb776-538e-495a-ae5d-11d1e190dc03">

<img width="783" alt="image" src="https://github.com/user-attachments/assets/9671c1f6-dda2-4280-abe1-8baa19ec07ef">


<img width="1008" alt="image" src="https://github.com/user-attachments/assets/897adf51-c621-4d71-8198-d5d34cd9b3f7">


<img width="1004" alt="image" src="https://github.com/user-attachments/assets/0bd5e81b-f7d6-4336-a3bd-1d7d47d85465">


<img width="989" alt="image" src="https://github.com/user-attachments/assets/b891c34a-a367-4416-9174-8a1a7d4056df">


<img width="943" alt="image" src="https://github.com/user-attachments/assets/28db21bd-ca93-4f39-95a5-2a74c096d2ee">

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql-headless
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          volumeMounts:
            - name: mysql-data
              mountPath: /var/lib/mysql
  volumeClaimTemplates:
    - metadata:
        name: mysql-data
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```


<img width="1001" alt="image" src="https://github.com/user-attachments/assets/683cf60b-9c59-4b79-8f2b-10801a41d52c">


<img width="1014" alt="image" src="https://github.com/user-attachments/assets/8f499191-29b8-4b3b-b7e9-9e32a765ccb4">


<img width="878" alt="image" src="https://github.com/user-attachments/assets/8edce9d3-457a-4b2d-bcbe-4b22283f0fef">





