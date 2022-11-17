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

## tcpdump network traffic to some pod

If you're debugging a deployment, first make sure it runs only 1 replica to simplify the process.

1. Find out what node the pod runs on.
1. SSH, connect, ... to that node and make sure tcpdump is installed.
1. Look up the pod's IP.
1. Run `tcpdump -i any -s 0 -A 'host <IP> and tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420'` to capture and log all HTTP GET requests.
    - `0x47455420` is `GET ` (note the space at the end) in hex.
    - You can capture other methods by replacing this by the corresponding hex sequence, but note that it must be 1, 2 or 4 bytes long.
