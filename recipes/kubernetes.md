# Kubernetes

## Expose an external service inside the cluster

For example to make an application more portable or to route traffic over an ingress.

Define a service of type `ExternalName`:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ExternalName
  externalName: service.somehost.com
```
The service can now be used from within the cluster by the specified name.

**Caveat:** HTTP(S) specifies the `Host` header. Additional configuration may be required to make this work.
The [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) supports below annotation that you can set on an Ingress to support these services:
```yaml
annotations:
  nginx.ingress.kubernetes.io/upstream-vhost: service.somehost.com
```
