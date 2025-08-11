
External Secrets Operator is a Kubernetes operator that integrates external secret management systems like AWS Secrets Manager, HashiCorp Vault, Google Secrets Manager, Azure Key Vault, IBM Cloud Secrets Manager, CyberArk Conjur and many more.

ou deploy the External Secrets Operator, it defines and creates a set of Custom Resource Definitions (CRDs) that extend the Kubernetes API. These CRDs are the core of how you interact with the operator. The key CRDs it defines are:

ExternalSecret: This is the most frequently used CRD. It's the blueprint that tells the operator which secret to fetch from your external secret manager and how to create the corresponding native Kubernetes Secret.

SecretStore: This CRD defines the connection to your external secret provider. It holds the authentication and configuration details, such as the provider type (e.g., AWS Secrets Manager, HashiCorp Vault), the server address, and the credentials needed to access it. This is a namespace-scoped resource.

ClusterSecretStore: This is similar to the SecretStore but is a global, cluster-wide resource. It can be referenced by ExternalSecret resources in any namespace. This is useful for providing a single, central gateway to your secret provider for all applications in your cluster.

The operator also defines other CRDs like PushSecret and ClusterExternalSecret, which are used for more advanced use cases like migrating secrets or managing secrets across multiple namespaces.


