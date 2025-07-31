
![Kyveno](kyverno.png)


✅ 1. Install Kyverno using Helm (Recommended)
Best for production clusters or if you’re already using Helm.

Step-by-step:
bash
Copy
Edit
# Add the Kyverno Helm repository
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update

# Install Kyverno into the "kyverno" namespace
helm install kyverno kyverno/kyverno --namespace kyverno --create-namespace
Verify installation:
bash
Copy
Edit
kubectl get pods -n kyverno
You should see Kyverno pods running.

✅ 2. Install Kyverno using kubectl (Quick Start)
Good for testing or small setups.

bash
Copy
Edit
kubectl create -f https://raw.githubusercontent.com/kyverno/kyverno/main/config/install.yaml
To uninstall:

bash
Copy
Edit
kubectl delete -f https://raw.githubusercontent.com/kyverno/kyverno/main/config/install.yam
