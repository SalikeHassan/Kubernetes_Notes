<img width="1101" alt="image" src="https://github.com/user-attachments/assets/09af4818-cb45-4085-8fed-0f71f39b784b">

<img width="1018" alt="image" src="https://github.com/user-attachments/assets/fc1b60ae-a8aa-4d5b-b416-b2184c30945b">

```yaml
Create a Persistent Volume Claim

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  storageClassName: azurefile-csi
```

```yaml
Deploy a Workload Using the PVC

apiVersion: apps/v1
kind: Deployment
metadata:
  name: shared-storage-demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: storage-demo
  template:
    metadata:
      labels:
        app: storage-demo
    spec:
      containers:
      - name: storage-demo
        image: mcr.microsoft.com/powershell
        command: ["pwsh", "-Command"]
        args: ["while($true) { Start-Sleep -s 3600; }"]
        volumeMounts:
        - mountPath: "/mnt/storage"
          name: azurefile-volume
      volumes:
      - name: azurefile-volume
        persistentVolumeClaim:
          claimName: azurefile-pvc
```
<img width="1044" alt="image" src="https://github.com/user-attachments/assets/1f77ed47-5f60-4791-87e4-cf443fd6339a">

<img width="1018" alt="image" src="https://github.com/user-attachments/assets/313fc824-f770-4839-af43-b44e7952481b">

```bash
az aks update --name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --enable-file-driver

az aks update --name myAKSCluster --resource-group myResourceGroup --enable-file-driver

kubectl get storageclass

kubectl describe storageclass <STORAGE_CLASS_NAME>

kubectl describe storageclass azurefile-csi

kubectl exec -it <POD_NAME> -- pwsh
Get-Content /mnt/storage/date.txt

az aks update --name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --enable-file-driver
```


