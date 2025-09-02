FluxCD works with three resources:

HelmRepository → where the chart comes from

HelmChart → which chart/version to pull (usually auto-generated)

HelmRelease → how to deploy the chart

1️⃣ HelmRepository

Kyverno publishes its Helm charts on GitHub Pages:
👉 https://kyverno.github.io/kyverno/

Define the repo in Flux:

apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: HelmRepository
metadata:
  name: kyverno-repo
  namespace: flux-system
spec:
  interval: 1h
  url: https://kyverno.github.io/kyverno/

2️⃣ HelmChart (optional)

Flux will auto-create this when you make a HelmRelease, but you can define it explicitly if you want fine control (e.g. chart version pinning).

apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: HelmChart
metadata:
  name: kyverno-chart
  namespace: flux-system
spec:
  chart: kyverno
  version: 3.1.0   # <-- choose a Kyverno version
  sourceRef:
    kind: HelmRepository
    name: kyverno-repo
    namespace: flux-system
  interval: 1h

3️⃣ HelmRelease

This tells Flux to actually install Kyverno into your cluster:

apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: kyverno
  namespace: kyverno
spec:
  interval: 5m
  chart:
    spec:
      chart: kyverno
      version: 3.1.0   # match the chart version above
      sourceRef:
        kind: HelmRepository
        name: kyverno-repo
        namespace: flux-system
  install:
    createNamespace: true
  values:
    replicaCount: 2
    admissionController:
      resources:
        requests:
          cpu: 100m
          memory: 256Mi
        limits:
          cpu: 500m
          memory: 512Mi


This deploys Kyverno into a namespace called kyverno.

values: section lets you override chart values (like scaling and resource requests).

🔄 Workflow Recap

HelmRepository → points to Kyverno chart repo.

HelmChart → resolves the chart (kyverno version 3.1.0) from that repo.

HelmRelease → installs Kyverno into the cluster using the chart.

Flux controllers will keep everything in sync automatically (check every interval).
