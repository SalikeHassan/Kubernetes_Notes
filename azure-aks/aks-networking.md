<img width="976" alt="image" src="https://github.com/user-attachments/assets/8a1171be-d010-4e93-a29b-1bc300218b7b">

<img width="961" alt="image" src="https://github.com/user-attachments/assets/5927ce68-348d-4fc7-9b84-3fdd5c0efbb1">


# Networking Scenarios in Kubernetes with Azure CNI

| **Scenario**              | **Example Organization IPs**   | **Overlay Networking (Azure CNI Overlay)**               | **Flat Networking (Azure CNI Node Subnet)**              | **Key Points**                                                                                     |
|---------------------------|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Corporate Network**      | 192.168.0.0/16                | No overlap (independent pod CIDR, e.g., 10.244.0.0/16).  | Ensure the VNet does not overlap with 192.168.0.0/16.    | Overlay uses a separate IP range for pods, while Flat uses the VNet’s range directly.             |
| **VNet CIDR**              | 10.1.0.0/16                  | VNet: 10.1.0.0/16<br>Pod CIDR: 10.244.0.0/16 (Overlay).  | VNet: 10.1.0.0/16.<br>Pods share IPs from the VNet.      | In Overlay, the pod range is independent. In Flat, the VNet range must accommodate pods.          |
| **Node Subnet**            | 10.1.1.0/24                  | Node Subnet: 10.1.1.0/24.                                | Node Subnet: 10.1.1.0/22 (e.g., ~1000 IPs).              | Flat needs a larger node subnet to include pods. Overlay pods don’t use VNet IPs.                |
| **Pod Range**              | N/A (managed by Overlay)      | Pod CIDR: 10.244.0.0/16.                                 | Pods share Node Subnet IPs.                              | Overlay separates pod IPs from the VNet. Flat directly uses VNet IPs, so VNet size is critical.   |
| **Ingress Subnet**         | 10.1.2.0/28                  | 10.1.2.0/28.                                             | 10.1.2.0/28.                                             | Ingress subnet (for load balancers) is common for both models.                                    |
| **Private Endpoint Subnet**| 10.1.3.0/28                  | 10.1.3.0/28.                                             | 10.1.3.0/28.                                             | Private endpoints (e.g., Key Vault) need a small, separate subnet.                                |

<img width="1184" alt="image" src="https://github.com/user-attachments/assets/49a0a7ff-28e4-479a-b352-d99c2d544257">

# Networking Setup for AKS: Overlay and Flat Networking

## 1. Overlay Networking Setup (Azure CNI Overlay)

### Scenario
An enterprise **e-commerce application** with high scalability needs. Pods do not require direct connectivity to external systems, so **Overlay Networking** is used to conserve IP addresses.

### Steps to Set Up Overlay Networking

1. **Create a Virtual Network**
   - Address Space: `10.1.0.0/16`
   - Subnet for Nodes: `10.1.1.0/24`
   - Example Command:
     ```bash
     az network vnet create \
         --name MyAKSVnet \
         --resource-group MyResourceGroup \
         --address-prefixes 10.1.0.0/16 \
         --subnet-name MyNodeSubnet \
         --subnet-prefix 10.1.1.0/24
     ```

2. **Enable Overlay Networking**
   - Specify the **Pod CIDR range** (e.g., `10.244.0.0/16`).
   - Example Command:
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

3. **Deploy the Application**
   - Deploy your application pods. They will receive IPs from the **Pod CIDR (`10.244.0.0/16`)**.

4. **Verify Pod and Node Networking**
   - Check pod IPs:
     ```bash
     kubectl get pods -o wide
     ```
   - Pod IPs will be in the range `10.244.x.x`.

5. **Connect to External Systems**
   - Use **NAT** for pod-to-network communication.
   - Example: A pod accessing Azure SQL Database will use the node's IP.
     
### Benefits of Overlay Networking
- Conserves VNet IPs, ideal for large-scale deployments.
- Simplifies IP management since pods use a separate CIDR block.

## 2. Flat Networking Setup (Azure CNI Node Subnet)

### Scenario
An enterprise **finance application** where pods must connect **directly** to an on-premises database or another Azure VNet without NAT.

### Steps to Set Up Flat Networking

1. **Create a Virtual Network**
   - Address Space: `10.1.0.0/16`
   - Subnet for Nodes: `/22` subnet for nodes and pods.
   - Example Command:
     ```bash
     az network vnet create \
         --name MyAKSVnet \
         --resource-group MyResourceGroup \
         --address-prefixes 10.1.0.0/16 \
         --subnet-name MyNodeSubnet \
         --subnet-prefix 10.1.1.0/22
     ```

2. **Enable Flat Networking**
   - Pods will receive IPs directly from the **Node Subnet (`10.1.1.0/22`)**.
   - Example Command:
     ```bash
     az aks create \
         --resource-group MyResourceGroup \
         --name MyAKSCluster \
         --vnet-subnet-id "/subscriptions/{sub_id}/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAKSVnet/subnets/MyNodeSubnet" \
         --network-plugin azure \
         --network-policy azure
     ```

3. **Deploy the Application**
   - Deploy your application pods. They will receive IPs directly from the **Node Subnet (`10.1.1.0/22`)**.

4. **Verify Pod and Node Networking**
   - Check pod IPs:
     ```bash
     kubectl get pods -o wide
     ```
   - Pod IPs will be in the range `10.1.1.x`.

5. **Connect to External Systems**
   - No NAT is required. Pods can directly access on-premises systems or other VNets using the VNet’s private connectivity.
   - Example: Pods accessing an on-premises SQL database via an ExpressRoute connection.

### Benefits of Flat Networking
- Provides **direct connectivity** for pods to external systems without NAT.
- Ideal for scenarios where pods need to communicate with on-premises or hybrid environments.

## 3. Comparison: Overlay vs. Flat Networking for Enterprise Applications

| **Feature**             | **Overlay Networking**                           | **Flat Networking**                          |
|-------------------------|-------------------------------------------------|---------------------------------------------|
| **IP Addressing**        | Pods use a separate CIDR range (e.g., `10.244.0.0/16`). | Pods share VNet IPs (e.g., `10.1.1.0/22`).   |
| **Scalability**          | Scales easily without consuming VNet IPs.        | Requires a large VNet CIDR block for scaling.|
| **Connectivity**         | Uses NAT for pod-to-network communication.       | Direct pod connectivity to VNet and beyond.  |
| **Best for**             | Large-scale apps with limited VNet IPs.          | Apps needing direct pod-to-network access.   |

## Summary
1. **Overlay Networking**:
   - Best for conserving VNet IPs in **large-scale deployments**.
   - Pods use a **separate CIDR range** for IPs.
   - Example: E-commerce application with thousands of pods.

2. **Flat Networking**:
   - Best for **direct connectivity** between pods and other networks or systems.
   - Pods share IPs from the VNet.
   - Example: Finance application connecting to an on-premises database.
  
# Designing IP Addressing for AKS
<img width="990" alt="image" src="https://github.com/user-attachments/assets/59349337-f7fc-4529-9ec7-37090d2527a5">

<img width="1006" alt="image" src="https://github.com/user-attachments/assets/f11ccb5a-bc02-42f5-b32f-53ebd7399868">

<img width="1046" alt="image" src="https://github.com/user-attachments/assets/07a3fb20-0bc2-49d0-90a9-52d2bfc17691">

<img width="1028" alt="image" src="https://github.com/user-attachments/assets/d73abde1-57e9-4fa6-8600-76b6567268ad">








