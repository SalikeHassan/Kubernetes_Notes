# Kubernetes Services

Services in Kubernetes enable communication between pods, external systems, and users. They abstract the complexity of networking and provide stable endpoints for dynamic workloads.

## Types of Kubernetes Services

1. **NodePort**
   - Exposes a service on a static port on each node in the cluster.
   - Accessible externally using `<NodeIP>:<NodePort>`.
   - **Default port range**: 30000-32767.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
```

2. **ClusterIP**
   - Default service type.
   - Provides a stable virtual IP address within the cluster.
   - Used for internal communication between pods.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

3. **LoadBalancer**
   - Provisions an external load balancer (e.g., cloud providers).
   - Routes external traffic to services.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## Kubernetes Ingress

Ingress provides HTTP and HTTPS routing to services within a Kubernetes cluster. It allows you to consolidate routing rules, SSL termination, and host-based routing.

### Key Features of Ingress

1. Routes external HTTP(S) traffic to services based on:
   - **Hostnames**
   - **URL paths**
2. Implements SSL/TLS termination at the cluster level.
3. Reduces the need for multiple NodePort or LoadBalancer services.

### Ingress Example
#### Basic Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: basic-ingress
spec:
  rules:
    - host: my-online-store.com
      http:
        paths:
          - path: /wear
            pathType: Prefix
            backend:
              service:
                name: wear-service
                port:
                  number: 80
          - path: /watch
            pathType: Prefix
            backend:
              service:
                name: watch-service
                port:
                  number: 80
```

### TLS Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-ingress
spec:
  tls:
    - hosts:
        - my-online-store.com
      secretName: tls-secret
  rules:
    - host: my-online-store.com
      http:
        paths:
          - path: /wear
            pathType: Prefix
            backend:
              service:
                name: wear-service
                port:
                  number: 80
```
### Rewrite Target Example
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rewrite-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: my-online-store.com
      http:
        paths:
          - path: /pay
            pathType: ImplementationSpecific
            backend:
              service:
                name: pay-service
                port:
                  number: 8080
```

## Comparison of Service Types

| **Feature**            | **NodePort**        | **ClusterIP**     | **LoadBalancer**      | **Ingress**             |
|-------------------------|---------------------|--------------------|------------------------|--------------------------|
| **External Access**     | Yes                | No                | Yes                   | Yes                     |
| **Internal Access**     | Yes                | Yes               | Yes                   | Yes                     |
| **Cost**                | Free               | Free              | High (Cloud LB)       | Cost-effective          |
| **SSL Termination**     | No                 | No                | Yes (via LB)          | Yes                     |
| **Path-Based Routing**  | No                 | No                | No                    | Yes                     |
| **Custom DNS**          | Yes (via node IP)  | No                | Yes (via LB IP)       | Yes                     |

### Commands
```bash
kubectl apply -f service.yaml

kubectl get services

kubectl describe service <service-name>

kubectl apply -f ingress.yaml

kubectl get ingress

kubectl describe ingress <ingress-name>

kubectl logs -n ingress-nginx <pod-name>

curl -H "Host: my-online-store.com" http://<ingress-controller-ip>/wear
```




