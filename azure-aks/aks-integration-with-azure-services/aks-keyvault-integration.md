<img width="942" alt="image" src="https://github.com/user-attachments/assets/4abe65b6-10ab-4981-91f2-f537900658a6">

```bash
Commands
Enable the Secrets Store CSI Driver
  az aks enable-addons --addons azure-keyvault-secrets-provider --name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP>
  az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup

Enable RBAC on Key Vault
  az keyvault update --name <KEY_VAULT_NAME> --resource-group <RESOURCE_GROUP> --enable-rbac-authorization
  az keyvault update --name myKeyVault --resource-group myResourceGroup --enable-rbac-authorization

Assign Workload Identity Access to Key Vault
  Retrieve Variables
    MANAGED_IDENTITY_PRINCIPAL_ID=$(az identity show --name <IDENTITY_NAME> --resource-group <RESOURCE_GROUP> --query "principalId" -o tsv)
    KEY_VAULT_RESOURCE_ID=$(az keyvault show --name <KEY_VAULT_NAME> --resource-group <RESOURCE_GROUP> --query "id" -o tsv)

Create Role Assignment
  az role assignment create --assignee-object-id <MANAGED_IDENTITY_PRINCIPAL_ID> --role "Key Vault Secrets User" --scope <KEY_VAULT_RESOURCE_ID>
  az role assignment create --assignee-object-id $MANAGED_IDENTITY_PRINCIPAL_ID --role "Key Vault Secrets User" --scope $KEY_VAULT_RESOURCE_ID
```

```yaml
Create a Secret Provider Class
secretstore.yaml

apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-keyvault
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"
    userAssignedIdentityID: <MANAGED_IDENTITY_CLIENT_ID>
    keyvaultName: <KEY_VAULT_NAME>
    cloudName: "AzurePublicCloud"
    objects: |
      array:
        - |
          objectName: mySecret
          objectType: secret
          objectVersion: ""
```

```yaml
Update Pod Configuration to Mount Secrets
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app-container
    image: my-app-image
    volumeMounts:
    - name: secrets-store
      mountPath: "/mnt/secrets"
      readOnly: true
  volumes:
  - name: secrets-store
    csi:
      driver: secrets-store.csi.k8s.io
      readOnly: true
      volumeAttributes:
        secretProviderClass: azure-keyvault

kubectl expose pod <POD_NAME> --type=LoadBalancer --port=80 --target-port=8080
kubectl expose pod app-pod --type=LoadBalancer --port=80 --target-port=8080
```
<img width="959" alt="image" src="https://github.com/user-attachments/assets/d0d6cdd4-e890-4952-a22f-66d60fa2f5ec">

<img width="1010" alt="image" src="https://github.com/user-attachments/assets/5cda62f5-69f6-4b41-b6cd-80ac50bb6606">


