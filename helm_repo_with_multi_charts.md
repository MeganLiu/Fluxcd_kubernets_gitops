

📂 Helm repository structure

A Helm repository is not organized by chart folders.
Instead, it’s just a flat folder with:

index.yaml (auto-generated metadata)

chart .tgz packages

Example if you want Kyverno and Nginx in the same repo (sds-helm):

sds-helm/
  index.yaml
  kyverno-3.5.0.tgz
  kyverno-3.5.1.tgz
  nginx-1.2.3.tgz
  nginx-1.2.4.tgz


Each chart package is a .tgz file produced by helm package.

🚀 How to add multiple charts

Package your charts:

helm package ./kyverno
helm package ./nginx


This will generate kyverno-3.5.0.tgz and nginx-1.2.3.tgz.

Upload both .tgz files to the same sds-helm repo in Artifactory.

Regenerate/update the index.yaml so both charts are included:

helm repo index . --url https://articat/artifactory/sds-helm


This command must be run in the folder with your .tgz files.

Push the new index.yaml back to sds-helm/.

✅ After that:

Your repo (sds-helm) will contain both Kyverno and Nginx.

You can install either one:

helm install kyverno sds-helm/kyverno --version 3.5.0
helm install nginx sds-helm/nginx --version 1.2.3


Flux will also be able to discover both from the single HelmRepository definition.

👉 So to answer:
You do not create sds-helm/nginx/ or sds-helm/kyverno/ folders.
You keep all .tgz packages together under sds-helm/ and let index.yaml handle the metadata.
