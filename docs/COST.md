# Cost Estimate

## AWS EC2

This capstone uses 3 small EC2 instances:

- 1 control-plane node
- 2 worker nodes

Estimated monthly cost depends on the chosen EC2 instance type, storage size, and region.

## Storage

PostgreSQL uses a Kubernetes PVC backed by node storage/local-path storage for this capstone.

## Network

Ingress traffic is routed through Traefik on the cluster nodes. No managed load balancer is required for the basic setup.

## Cost Reduction

To reduce cost by half, the cluster can be stopped when not in use, smaller instance types can be used, and non-essential monitoring components can be removed after demo. For production, managed services may increase cost but reduce operational burden.
