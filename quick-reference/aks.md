<img width="925" alt="image" src="https://github.com/user-attachments/assets/9edbc3b7-eacc-4ce1-9e9f-e85104ecd9e1" />

<img width="999" alt="image" src="https://github.com/user-attachments/assets/47a3a544-1277-47b0-84a1-3cdacbc5a05a" />

<img width="1063" alt="image" src="https://github.com/user-attachments/assets/c116848b-fccc-42ef-838b-9a0c6d59ad2a" />

<img width="961" alt="image" src="https://github.com/user-attachments/assets/706ee8d5-9287-4717-bb61-245b24719231" />

---
<img width="976" alt="image" src="https://github.com/user-attachments/assets/4f8d451a-f756-46d0-ae01-672aabcd7ee0" />

<img width="1004" alt="image" src="https://github.com/user-attachments/assets/317e2987-f08f-447e-91ea-cc5e8ee2a61d" />

<img width="804" alt="image" src="https://github.com/user-attachments/assets/91e84773-60a4-468c-b333-c593faa65988" />

![image](https://github.com/user-attachments/assets/52f1e4e9-b754-46c1-8d73-c470cb16351f)

### Overlay Network

```bash
az network vnet create \
    --name MyAKSVnet \
    --resource-group MyResourceGroup \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name MyNodeSubnet \
    --subnet-prefix 10.1.1.0/24
```
```bash
az aks create \
    --resource-group MyResourceGroup \
    --name MyAKSCluster \
    --vnet-subnet-id "/subscriptions/{sub_id}/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAKSVnet/subnets/MyNodeSubnet" \
    --network-plugin azure \
    --network-policy azure \
    --pod-cidr 10.244.0.0/16 \
    --service-cidr 10.2.0.0/16 \
    --dns-service-ip 10.2.0.10
```

### Flat Network
```bash
az network vnet create \
    --name MyAKSVnet \
    --resource-group MyResourceGroup \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name MyNodeSubnet \
    --subnet-prefix 10.1.1.0/22
```
```bash
az aks create \
    --resource-group MyResourceGroup \
    --name MyAKSCluster \
    --vnet-subnet-id "/subscriptions/{sub_id}/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAKSVnet/subnets/MyNodeSubnet" \
    --network-plugin azure \
    --network-policy azure
```

![image](https://github.com/user-attachments/assets/dcb6f1e6-282e-48e1-99af-68d8b89f57f6)

---
# Azure Resource Access
<img width="987" alt="image" src="https://github.com/user-attachments/assets/761415ef-6bf5-4d15-8884-6a555d6e7546" />

<img width="993" alt="image" src="https://github.com/user-attachments/assets/d58b0ebd-c9a2-44af-9473-cf9ff25ac43c" />

<img width="1012" alt="image" src="https://github.com/user-attachments/assets/f2420fb1-fc78-4d40-8ee1-fe0532ad28d8" />

<img width="1032" alt="image" src="https://github.com/user-attachments/assets/544f4d6d-9161-451d-8a22-fc86c3c9698f" />









