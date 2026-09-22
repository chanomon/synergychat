# synergychat
A chat, deployed with kubernetes, using good practices of kubernetes, following the Bootdev courses


Install prerequisites

- kubectl
- Kubernetes cluster/runtime
- Container runtime and registry access, if building images

Configure cluster access
```
    kubectl config current-context
    kubectl get nodes
```
Create the namespace
```
    kubectl apply -f namespace.yaml
```
Create prerequisites
- Secrets
- ConfigMaps
- PersistentVolumes/PersistentVolumeClaims
- ServiceAccounts/RBAC
- Ingress controller or storage classes, if required
```
    kubectl apply -f configmaps/
    kubectl apply -f secrets/
```
Build and publish images
Ensure image names and tags in your Deployments exist in the new machine’s registry. Avoid relying on local-only images unless the cluster uses that same runtime.

Deploy workloads

```
    kubectl apply -f deployments/
```

Create services

```
    kubectl apply -f services/
```

Apply autoscaling

```
    kubectl apply -f hpa/
```
Confirm Deployments define CPU/memory resource requests; HPA commonly depends on them.

Apply ingress and policies

```
    kubectl apply -f ingress/
    kubectl apply -f network-policies/
```
Verify

```
kubectl get all -n your-namespace
kubectl get events -n your-namespace --sort-by=.lastTimestamp
kubectl describe pod <pod-name> -n your-namespace
kubectl logs <pod-name> -n your-namespace
```
Test externally
Check service endpoints, DNS, TLS certificates, database connectivity, and application health endpoints.

For repeatable deployments, consider putting everything in a declarative tool such as Helm or Kustomize rather than relying on filename order. Also document external dependencies that YAML cannot create automatically, such as DNS records, cloud load balancers, registry credentials, databases, and TLS issuers.

## To view the deployed application
First check how its Service is exposed:

```
kubectl get svc -n your-namespace
```
Common cases:

- ClusterIP: only reachable inside the cluster. Use port forwarding:
```
    kubectl port-forward svc/your-service 8080:80 -n your-namespace
```
Then open http://localhost:8080.

NodePort: open:
```
http://<node-ip>:<node-port>
```
LoadBalancer: get the external address:
```
    kubectl get svc your-service -n your-namespace
```

Open the displayed EXTERNAL-IP.

If using an Ingress:
```
kubectl get ingress -n your-namespace
```
Then open its hostname in your browser. Ensure DNS points to the ingress address and that an ingress controller is installed.

