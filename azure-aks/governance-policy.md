<img width="989" alt="image" src="https://github.com/user-attachments/assets/b5338f19-4194-41dc-ab94-81875365b386">


<img width="1079" alt="image" src="https://github.com/user-attachments/assets/ff764a26-0ee8-4eaf-90b8-968fbd5592a5">

<img width="1011" alt="image" src="https://github.com/user-attachments/assets/e05059ec-77ab-44e0-99e9-02f26e032cb7">

---
<img width="980" alt="image" src="https://github.com/user-attachments/assets/5d7d508e-8b11-49e8-829f-e11fe665a83a">
<img width="990" alt="image" src="https://github.com/user-attachments/assets/a496088f-b685-49ac-a696-9ec71b9eec08">

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privileged-pod
spec:
  containers:
  - name: nginx
    image: nginx
    securityContext:
      privileged: true
```
### The pod with securityContext.privileged: true was denied and later successfully deployed after removing the privileged field lies in the governance enforced by Azure Policy through the Gatekeeper admission controller in AKS.

<img width="1048" alt="image" src="https://github.com/user-attachments/assets/038c4b55-2056-4672-a126-bc209d04d672">

### Azure CLI Command to Enable Azure Policy Add-On

```bash
az aks enable-addons \
    --addons azure-policy \
    --resource-group <RESOURCE_GROUP_NAME> \
    --name <CLUSTER_NAME>
```
<img width="1111" alt="image" src="https://github.com/user-attachments/assets/ab7d59b6-99a7-471b-8957-cfe2ffb0f89d">
<img width="1059" alt="image" src="https://github.com/user-attachments/assets/62a75fff-e323-4b46-b684-450706d6ce79">
