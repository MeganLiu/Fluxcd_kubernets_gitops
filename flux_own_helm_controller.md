
► annotating HelmRepository kyverno in flux-system namespace

Flux first adds a special annotation (reconcile.fluxcd.io/requestedAt) to the HelmRepository object.

This forces the HelmRepository controller to re-fetch chart metadata from the upstream repo.

✔ HelmRepository annotated

Annotation applied successfully.

◎ waiting for HelmRepository reconciliation

Flux waits until the repo controller has finished fetching metadata.

✔ fetched revision sha256:5caf...

The repository was fetched successfully, and Flux shows the checksum of the fetched index (so you know which repo state it’s working with).

► annotating HelmChart flux-system-kyverno in flux-system namespace

Now Flux forces reconciliation of the HelmChart resource (this is the rendered chart from the Helm repo, ready to be installed).

✔ HelmChart annotated

Annotation applied successfully.

◎ waiting for HelmChart reconciliation

Flux waits until the HelmChart controller has finished pulling and preparing the chart.

## Which “HelmChart controller” is Flux talking about?

It’s Flux’s own Helm controller, not anything from the official Helm project.

The Helm CLI (helm install ...) is not running inside your cluster.

Instead, Flux has a controller called helm-controller, which is part of the Flux stack (helm-controller, kustomize-controller, source-controller, notification-controller).

The helm-controller is the component that:

Watches HelmRepository, HelmChart, and HelmRelease CRDs.

Pulls and renders Helm charts.

Installs/updates Helm releases inside the cluster.

So when you see:

◎ waiting for HelmChart reconciliation


## Flux is waiting for its own helm-controller to finish reconciling the HelmChart object.

🔹 How to check the logs

Find the helm-controller pod:

kubectl -n flux-system get pods | grep helm-controller


You’ll see something like:

helm-controller-7d8c9b65d9-abc12   1/1   Running   0   5h


Check its logs:

kubectl -n flux-system logs deploy/helm-controller


or if you want to follow in real-time:

kubectl -n flux-system logs deploy/helm-controller -f

🔹 Debugging a specific HelmChart

If you want details about the HelmChart resource itself:

kubectl -n flux-system describe helmchart flux-system-kyverno


This will show events, last fetched revision, errors, etc.

✅ Summary:

The “HelmChart reconciliation” is handled by Flux’s helm-controller, not the official Helm binary.

You can check its behavior with kubectl logs -n flux-system deploy/helm-controller.
