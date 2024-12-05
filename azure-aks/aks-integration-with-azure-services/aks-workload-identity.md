<img width="1028" alt="image" src="https://github.com/user-attachments/assets/8c4a75fd-757a-4462-b60c-eec38f55fe1e">

```bash
Commands
Enable OIDC Issuer and Workload Identity on AKS
  az aks update --name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --enable-oidc-issuer --enable-workload-identity
  az aks update --name myAKSCluster --resource-group myResourceGroup --enable-oidc-issuer --enable-workload-identity
Retrieve the OIDC Issuer URL
  az aks show --name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --query "oidcIssuerProfile.issuerUrl" -o tsv
  az aks show --name myAKSCluster --resource-group myResourceGroup --query "oidcIssuerProfile.issuerUrl" -o tsv

Create a User-Assigned Managed Identity
  az identity create --name <IDENTITY_NAME> --resource-group <RESOURCE_GROUP> --location <LOCATION>
  az identity create --name myAppIdentity --resource-group myResourceGroup --location eastus

Annotate a Service Account
  kubectl annotate serviceaccount <SERVICE_ACCOUNT_NAME> azure.workload.identity/client-id=<MANAGED_IDENTITY_CLIENT_ID>
  kubectl annotate serviceaccount myAppServiceAccount azure.workload.identity/client-id=<client-id-value>

Create a Federated Identity Credential
  az identity federated-credential create --name <FEDERATED_CREDENTIAL_NAME> \
  --resource-group <RESOURCE_GROUP> --identity-name <IDENTITY_NAME> \
  --issuer <OIDC_ISSUER_URL> --subject <SERVICE_ACCOUNT_SUBJECT> --audiences api://AzureADTokenExchange

  az identity federated-credential create --name myFederatedCredential \
  --resource-group myResourceGroup --identity-name myAppIdentity \
  --issuer https://oidc-issuer-url/ --subject system:serviceaccount:default:myAppServiceAccount \
  --audiences api://AzureADTokenExchange

Assign a Role to the Managed Identity
  az role assignment create --assignee-object-id <MANAGED_IDENTITY_PRINCIPAL_ID> \
  --role "Storage Blob Data Reader" --scope <STORAGE_ACCOUNT_RESOURCE_ID>

  az role assignment create --assignee-object-id <principal-id> \
  --role "Storage Blob Data Reader" --scope /subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    azure.workload.identity/use: "true"
spec:
  serviceAccountName: myAppServiceAccount
  containers:
  - name: my-app-container
    image: my-app-image
    ports:
    - containerPort: 8080
```

```bash
Expose the Pod
  kubectl expose pod <POD_NAME> --type=LoadBalancer --port=80 --target-port=8080
  kubectl expose pod my-app-pod --type=LoadBalancer --port=80 --target-port=8080
```
<img width="1077" alt="image" src="https://github.com/user-attachments/assets/ca8535c9-4449-47a3-87b4-f27c513aa0d3">
<img width="1006" alt="image" src="https://github.com/user-attachments/assets/7e1cf04d-5449-446f-b64c-7bfcd7cd4964">








