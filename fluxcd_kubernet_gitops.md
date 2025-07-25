


![My Diagram](flux-bootstrap-diagram.png)


Bootstrap with Flux CLI
The flux bootstrap command deploys the Flux controllers on Kubernetes cluster(s) and configures the controllers to sync the cluster(s) state from a Git repository. Besides installing the controllers, the bootstrap command pushes the Flux manifests to the Git repository and configures Flux to update itself from Git.

Cluster domain
The Flux components are communicating over HTTP with source-controller and notification-controller. To reduce the DNS queries performed by each component, the cluster internal domain name is used to compose the FQDN of each service e.g. http://source-controller.flux-system.svc.cluster.local./.

The cluster domain name can be set using the --cluster-domain flag.

flux bootstrap git \
  --cluster-domain cluster.internal
When not specified, the cluster domain defaults to cluster.local.

