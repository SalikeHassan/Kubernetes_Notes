### Ingress
Ingress in Kubernetes is an API object that manages external access to services within a Kubernetes cluster. It acts as an HTTP routing layer, directing traffic from outside the cluster to internal services based on predefined rules.

### Why Ingress?
While Kubernetes Services (like NodePort and LoadBalancer) expose a single service to external traffic, they lack advanced traffic routing capabilities. Ingress fills this gap, offering more sophisticated routing options.

### How Ingress Work?
Ingress directs traffic from outside the cluster to internal services based on predefined rules. These rules are defined in Ingress Resources. However, Ingress alone cannot route incoming traffic to its respective service; this requires an Ingress Controller.

**Ingress Controller:** A pod that watches for Ingress resources and enforces the rules. It acts as a gateway, managing and controlling the traffic entering the cluster.

There are multiple Ingress controller options available, such as NGINX, HAProxy, and Traefik.

### Ingress Resource
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: welcome-ingress
spec:
  rules:
  - host: welcome.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: welcome-service
            port:
              number: 80
```
Like other Kubernetes objects, the Ingress rule YAML above has four properties: apiVersion, kind, metadata, and spec. Here, our focus is to understand the YAML properties under the spec section.

- **rules:** A list of rules for routing incoming requests. Each rule allows defining how traffic is directed to different services within the cluster.
- **host:** Specifies the host (domain) on which the rule applies. The Ingress controller uses this to match requests with the host header *welcome.example.com*. (It's optional)
- **http:** Indicates that the rule is applicable to HTTP traffic.
- **paths:** A list of paths (for example, /testpath), each of which has an associated backend defined with a *service.name* and a *service.port.name* or *service.port.number*.
- **pathType:** Determines how the path is matched. Prefix means the rule matches this path and any subpaths (e.g., /, /about, /contact). There are two other pathType like *ImplementationSpecific*, and *Exact*.  For more details on pathType and match examples, check https://kubernetes.io/docs/concepts/services-networking/ingress/
- **backend:** Specifies the backend service to route the traffic to if the rule matches.
- **service:** Indicates that the backend is a Kubernetes Service.
name: welcome-service: The name of the Service to route traffic to. This Service should exist in the same namespace as the Ingress.
- **port:** Specifies the service port to route traffic to.
- **number:** The target port on the welcome-service to which the traffic should be directed.

**Note:** 
- Multiple hosts are possible
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-host-ingress
spec:
  rules:
  - host: webapp1.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp1-service
            port:
              number: 80
  - host: galaxy.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: galaxy-service
            port:
              number: 80
```
- Multiple paths are possible for the same domain
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-path-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /webapp1
        pathType: Prefix
        backend:
          service:
            name: webapp1-service
            port:
              number: 80
      - path: /galaxy
        pathType: Prefix
        backend:
          service:
            name: galaxy-service
            port:
              number: 80

```
My intention is to keep the notes simple and easy to understand. For more details on Ingress and its working demo, you can check my blog.<br>
**Blog link: https://medium.com/@salikehassan93/mastering-kubernetes-ingress-a-simple-guide-to-learn-and-practice-ingress-using-minikube-7194851e8bf2**
### In upcoming notes, we'll explore the Ingress's default backend service and TLS for enhanced security and functionality.
