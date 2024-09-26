Setting up K3s on RHEL involves several steps to ensure a smooth installation and configuration. Here’s a detailed guide based on your requirements:
### Installation of K3s

1. **Install K3s**:
   Run the following command to install K3s:
   ```bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik" sh -
   ```
   This command installs K3s with the server components and disables Traefik as the ingress controller.

2. **Configure Kubeconfig for Kubectl**:
   After installation, the Kubeconfig file for Kubectl is located at `/etc/rancher/k3s/k3s.yaml`. Copy this file to `~/.kube/config` to configure Kubectl:
   ```bash
   mkdir ~/.kube
   touch ~/.kube/config
   sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
   sudo chown $(id -u):$(id -g) ~/.kube/config
   sudo chmod 600 ~/.kube/config
   ```
   Set the `KUBECONFIG` environment variable:
   ```bash
   vim ~/.bashrc
   export KUBECONFIG=~/.kube/config
   source ~/.bashrc
   ```

3. **Verify Kubectl Configuration**:
   Check the Kubectl configuration:
   ```bash
   kubectl version --output=yaml
   kubectl cluster-info
   ```

### Optional: Installing Specific K3s Versions

If you need to install a specific version of K3s, you can specify it during installation:
```bash
export INSTALL_K3S_VERSION=v1.28.10+k3s1
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --disable=traefik" sh -s -
```
Replace `v1.28.10+k3s1` with the version you want to install.

### Configuring Kubelet and Kubectl

To ensure compatibility with the installed K3s version, configure Kubectl and Kubelet appropriately:
```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
exclude=kubelet,kubeadm,kubectl,cri-tools,kubernetes-cni
EOF

sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
sudo systemctl enable --now kubelet
```
This configuration ensures that Kubectl and Kubelet are correctly installed and managed by systemd.


Installation script
```
#!/bin/bash

# Set the K3S version to install
# export INSTALL_K3S_VERSION=v1.28.11+k3s1

# Inform the user about the installation process
echo "Installing K3S version $INSTALL_K3S_VERSION..."

# Download and install K3S
curl -sfL https://get.k3s.io | sh -

# Create kube config directory and file
echo "Setting up Kubernetes configuration..."
mkdir -p ~/.kube
touch ~/.kube/config

# Copy K3S configuration to kube config
echo "Copying K3S configuration..."
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config

# Set appropriate ownership and permissions
echo "Setting permissions for kube config..."
sudo chown $(id -u):$(id -g) ~/.kube/config
sudo chmod 600 ~/.kube/config

# Update sudoers secure_path if needed
echo "Updating sudoers secure_path..."
sudo sed -i 's/^Defaults\s*secure_path.*/Defaults secure_path = \/sbin:\/bin:\/usr\/sbin:\/usr\/bin:\/usr\/local\/bin/g' /etc/sudoers

# Add KUBECONFIG to .bashrc if not already present
echo "Setting KUBECONFIG in .bashrc..."
echo "export KUBECONFIG=~/.kube/config" | grep -Fxq - /home/$(whoami)/.bashrc || echo "export KUBECONFIG=~/.kube/config" >> /home/$(whoami)/.bashrc

# Source .bashrc to apply changes
echo "Applying .bashrc changes..."
source /home/$(whoami)/.bashrc

# Verify kubectl version and cluster info
echo "Verifying kubectl version..."
kubectl version --output=yaml
echo "Checking Kubernetes cluster info..."
kubectl cluster-info

# Inform the user that the installation is complete
echo "K3S installation complete!"

```
### Conclusion

By following these steps, you can install and configure K3s on RHEL, set up Kubectl to interact with your K3s cluster, and optionally install specific versions of K3s as per your requirements. This setup provides a lightweight Kubernetes distribution suitable for various deployment scenarios.