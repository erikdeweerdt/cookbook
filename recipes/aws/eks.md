# EKS

# externalTrafficPolicy on a Load Balancer service

If the VPC in which nodes are spawned defines a DHCP option set that overrides the domain, `externalTrafficPolicy: Local` won't work.
This is because services are only considered local if their node name matches the host name.
Hence, `curl http://localhost:<healthCheckNodePort>/healthz` will always return 0 for `localEndpoints`, regardless of the node you run it on.
Health checks on the ELB will therefore fail on all nodes.

See https://github.com/kubernetes/kubernetes/issues/80579 and https://github.com/awslabs/amazon-eks-ami/issues/301
