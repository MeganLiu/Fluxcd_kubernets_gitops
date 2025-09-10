## After bootstrap fluxcd , how to check the created resource and configration?

What flux bootstrap does

When you run a command like:

flux bootstrap github \
  --owner=my-org \
  --repository=my-repo \
  --branch=main \
  --path=clusters/my-cluster


Flux creates:

GitRepository: points to the repo where Flux pulls the manifests from.

Kustomization: describes which paths/branches Flux should sync.

Providers / Secrets: e.g., SSH keys or Git tokens stored in Kubernetes secrets.

Optional HelmReleases if you added Helm.

## Flux configuration done via CRD
Flux does not store the original flux bootstrap CLI command anywhere. But you can infer most of the parameters by looking at the resources it created in the cluster, because flux bootstrap essentially just creates a set of CRs (Custom Resources) in the flux-system namespace.

Here’s how to do it:

#### Check the GitRepository
kubectl get gitrepositories -n flux-system -o yaml
spec.url → the Git repo URL (--repository / --owner)
spec.branch → branch being tracked (--branch)
spec.interval → sync interval

spec.secretRef → the secret holding Git credentials

spec.path → relative path in the repo (--path)

From this, you can infer most of the bootstrap parameters.

#### Check Kustomizations
kubectl get kustomizations -n flux-system -o yaml


This shows:

spec.path → the directory being applied

spec.prune → whether old resources are pruned

spec.interval → how often it syncs

spec.sourceRef → the GitRepository name

####
kubectl get crds | grep flux
alerts.notification.toolkit.fluxcd.io                      2025-06-23T12:48:43Z
buckets.source.toolkit.fluxcd.io                           2025-06-23T12:48:43Z
gitrepositories.source.toolkit.fluxcd.io                   2025-06-23T12:48:43Z
helmcharts.source.toolkit.fluxcd.io                        2025-06-23T12:48:43Z
helmreleases.helm.toolkit.fluxcd.io                        2025-06-23T12:48:43Z
helmrepositories.source.toolkit.fluxcd.io                  2025-06-23T12:48:44Z
kustomizations.kustomize.toolkit.fluxcd.io                 2025-06-23T12:48:44Z
ocirepositories.source.toolkit.fluxcd.io                   2025-06-23T12:48:44Z
providers.notification.toolkit.fluxcd.io                   2025-06-23T12:48:44Z
receivers.notification.toolkit.fluxcd.io                   2025-06-23T12:48:44Z
ChatGPT 说：

Perfect — this shows the CRDs installed by FluxCD in your cluster. Let’s break them down so you know what each one is for:
####
CRD	Controller / Purpose
alerts.notification.toolkit.fluxcd.io	Notification controller — defines alert rules for Flux events

buckets.source.toolkit.fluxcd.io	Source controller — represents a bucket (S3/OCI) to pull manifests from

gitrepositories.source.toolkit.fluxcd.io	Source controller — represents a Git repository to sync manifests from

helmcharts.source.toolkit.fluxcd.io	Source controller — represents a Helm chart from a Helm repository

helmreleases.helm.toolkit.fluxcd.io	Helm controller — deploys Helm charts to the cluster

helmrepositories.source.toolkit.fluxcd.io	Source controller — represents a Helm repository containing charts

kustomizations.kustomize.toolkit.fluxcd.io	Kustomize controller — applies Kustomize manifests from sources

ocirepositories.source.toolkit.fluxcd.io	Source controller — represents an OCI registry as a source for manifests

providers.notification.toolkit.fluxcd.io	Notification controller — defines notification targets (Slack, Teams, etc.)

receivers.notification.toolkit.fluxcd.io	Notification controller — receives external events (webhooks) to trigger alerts or syncs

## Gitrepositories and Kustomization relationship
Example:
# GitRepository
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: flux-system
  namespace: flux-system
spec:
  url: https://github.com/my-org/my-repo
  branch: main
  interval: 1m
---
# Kustomization
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: flux-system
  namespace: flux-system
spec:
  interval: 10m
  path: ./clusters/sds-production
  prune: true
  sourceRef:
    kind: GitRepository
    name: flux-system


The GitRepository fetches everything from the repo.

The Kustomization applies just ./clusters/sds-production to the cluster every 10 minutes and prunes deleted resources.

<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/81cffc4f-dbac-44e0-8c32-db23ef88bd4f" />

