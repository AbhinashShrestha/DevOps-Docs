cTo set up K3s and Rancher with Helm on your server and worker nodes, follow these steps:
### Step 1: Install Helm

1. Download and install Helm:

   ```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
   ```

### Step 2: Install K3s on Server Node

1. Install K3s on the server node:

   ```bash
   curl -sfL https://get.k3s.io | sh -
   ```

   Alternatively, specify a specific version:

   ```bash
   curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=v1.28.10+k3s1 sh -s - server --write-kubeconfig-mode 666
   ```

   This command installs K3s and sets up the server with a Kubernetes configuration file (`k3s.yaml`).

2. Export the Kubernetes configuration to `KUBECONFIG`:

   ```bash
   export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
   ```

### Step 3: Join Worker Node to K3s Cluster

1. Install K3s on the worker node:

   ```bash
   curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.9:6443 K3S_TOKEN=<token> sh -
   ```

   Replace `<token>` with the actual token generated during K3s server installation.

### Step 4: Install Rancher using Helm

1. Add Rancher Helm repository and install Cert-Manager:

   ```bash
   helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
   kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.14.5/cert-manager.crds.yaml
   helm repo add jetstack https://charts.jetstack.io
   helm repo update
   helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace
   ```

2. Install Rancher:

   ```bash
   helm install rancher rancher-stable/rancher \
     --namespace cattle-system \
     --set hostname=192.168.56.56.sslip.io \
     --set replicas=1 \
     --set bootstrapPassword=rancher
   ```

   Replace `192.168.56.9.sslip.io` with your actual Rancher hostname or IP address.

### Step 5: Verify Installation

1. Check the status of Cert-Manager pods:

   ```bash
   kubectl get pods --namespace cert-manager
   ```

   Ensure all pods (`cert-manager`, `cert-manager-webhook`, `cert-manager-cainjector`) are running.

2. Check the deployment of Rancher:

   ```bash
   kubectl -n cattle-system get deploy rancher
   ```
   
   Verify that the Rancher deployment (`rancher`) is deployed and ready.
   
3. Check cert-manager
```
kubectl get pods --namespace cert-manager
```
### Access Rancher

Once Rancher is installed and ready, access the Rancher UI:

- Rancher may take a few minutes to initialize. Once ready, access it via the hostname or IP you set (`https://192.168.56.9.sslip.io` in this example).

### Notes

- Ensure your Kubernetes configuration (`k3s.yaml`) is properly secured and accessible only to authorized users.
- Monitor the installation logs and Kubernetes events for any issues (`kubectl logs`, `kubectl describe`).
- Refer to Rancher and K3s documentation for more advanced configuration and troubleshooting steps as needed.