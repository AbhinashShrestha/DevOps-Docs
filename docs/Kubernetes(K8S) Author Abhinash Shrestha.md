## Step 1: Set Hostname
Configure the hostname of your system.
```sh
sudo hostnamectl set-hostname master01.mero.org
and in /etc/hosts
server_ip master01.mero.org master01
```

## Step 2: Disable Swap
Disable swap and ensure it does not turn back on after a reboot.
```sh
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

## Step 3: Load Required Kernel Modules
Load the necessary kernel modules for Kubernetes.
```sh
sudo modprobe overlay
sudo modprobe br_netfilter
echo -e "overlay\nbr_netfilter" | sudo tee /etc/modules-load.d/k8s.conf
```

## Step 4: Configure sysctl Parameters
Set up sysctl parameters needed by Kubernetes.
```sh
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

sudo sysctl --system
```

## Step 5: Install Containerd
Install and configure `containerd` as the container runtime.
```sh
sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
sudo dnf install containerd.io -y
sudo systemctl start containerd
sudo systemctl enable containerd

containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
sudo systemctl restart containerd
```

## Step 6: Add Kubernetes Repository
Add the Kubernetes repository to your package manager.
```sh
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF
```

## Step 7: Install Kubernetes Components
Install `kubelet`, `kubeadm`, and `kubectl`.
```sh
sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
sudo systemctl enable --now kubelet
```

## Step 8: Initialize Kubernetes Cluster
Initialize your Kubernetes cluster with `kubeadm`.
```sh
sudo kubeadm init --control-plane-endpoint=master01.mero.org --pod-network-cidr=10.244.0.0/16
```
Follow the post-initialization instructions provided by `kubeadm` output.

## Step 9: Configure kubectl for Your User
Set up `kubectl` configuration for the regular user.
```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step 10: Deploy Pod Network (Flannel)
Deploy the Flannel network add-on.
```sh
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

## Step 11: Join Worker Nodes
To join worker nodes to the cluster, run the following on each worker node:
```sh
do step 2 to 6 then 

(kubeadm token create --print-join-command) (in master)

kubeadm join master01.mero.org:6443 --token <token> \
--discovery-token-ca-cert-hash sha256:<hash>
```

## Step 12: Join Additional Control Plane Nodes
To join additional control plane nodes, run the following on each control plane node:
```sh
kubeadm join master01.mero.org:6443 --token <token> \
--discovery-token-ca-cert-hash sha256:<hash> \
--control-plane
```

Replace `<token>` and `<hash>` with the values provided by `kubeadm init`.

## Example Steps by Abhinash Shrestha on Rocky Linux 9.4

```
[vagrant@mero ~]$ sudo hostnamectl

 Static hostname: master01.mero.org

[vagrant@mero ~]$ sudo swapoff -a

[vagrant@mero ~]$ sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

[vagrant@mero ~]$ sudo modprobe overlay

[vagrant@mero ~]$ sudo modprobe br_netfilter

[vagrant@mero ~]$ sudo tee /etc/modules-load.d/k8s.conf <<EOF

overlay

br_netfilter

EOF

overlay

br_netfilter

[vagrant@mero ~]$ sudo tee /etc/sysctl.d/k8s.conf <<EOT

net.bridge.bridge-nf-call-iptables  = 1

net.ipv4.ip_forward                 = 1

net.bridge.bridge-nf-call-ip6tables = 1

EOT

net.bridge.bridge-nf-call-iptables  = 1

net.ipv4.ip_forward                 = 1

net.bridge.bridge-nf-call-ip6tables = 1

[vagrant@mero ~]$ sudo sysctl --system

* Applying /usr/lib/sysctl.d/10-default-yama-scope.conf ...

* Applying /usr/lib/sysctl.d/50-coredump.conf ...

* Applying /usr/lib/sysctl.d/50-default.conf ...

* Applying /usr/lib/sysctl.d/50-libkcapi-optmem_max.conf ...

* Applying /usr/lib/sysctl.d/50-pid-max.conf ...

* Applying /usr/lib/sysctl.d/50-redhat.conf ...

* Applying /etc/sysctl.d/99-sysctl.conf ...

* Applying /etc/sysctl.d/k8s.conf ...

* Applying /etc/sysctl.conf ...

kernel.yama.ptrace_scope = 0

kernel.core_pattern = |/usr/lib/systemd/systemd-coredump %P %u %g %s %t %c %h

kernel.core_pipe_limit = 16

fs.suid_dumpable = 2

kernel.sysrq = 16

kernel.core_uses_pid = 1

net.ipv4.conf.default.rp_filter = 2

net.ipv4.conf.eth0.rp_filter = 2

net.ipv4.conf.eth1.rp_filter = 2

net.ipv4.conf.lo.rp_filter = 2

net.ipv4.conf.default.accept_source_route = 0

net.ipv4.conf.eth0.accept_source_route = 0

net.ipv4.conf.eth1.accept_source_route = 0

net.ipv4.conf.lo.accept_source_route = 0

net.ipv4.conf.default.promote_secondaries = 1

net.ipv4.conf.eth0.promote_secondaries = 1

net.ipv4.conf.eth1.promote_secondaries = 1

net.ipv4.conf.lo.promote_secondaries = 1

net.ipv4.ping_group_range = 0 2147483647

net.core.default_qdisc = fq_codel

fs.protected_hardlinks = 1

fs.protected_symlinks = 1

fs.protected_regular = 1

fs.protected_fifos = 1

net.core.optmem_max = 81920

kernel.pid_max = 4194304

kernel.kptr_restrict = 1

net.ipv4.conf.default.rp_filter = 1

net.ipv4.conf.eth0.rp_filter = 1

net.ipv4.conf.eth1.rp_filter = 1

net.ipv4.conf.lo.rp_filter = 1

net.bridge.bridge-nf-call-iptables = 1

net.ipv4.ip_forward = 1

net.bridge.bridge-nf-call-ip6tables = 1


[vagrant@mero ~]$ sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo

Adding repo from: https://download.docker.com/linux/rhel/docker-ce.repo

[vagrant@mero ~]$ sudo dnf install containerd.io -y
[vagrant@mero ~]$ sudo systemctl start containerd
[vagrant@mero ~]$ sudo systemctl enable containerd
[vagrant@mero ~]$ containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1

[vagrant@mero ~]$ sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

[vagrant@mero ~]$ sudo systemctl restart containerd

[vagrant@mero ~]$ cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo

[kubernetes]

name=Kubernetes

baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/

enabled=1

gpgcheck=1

gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key

exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni

EOF

[kubernetes]

name=Kubernetes

baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/

enabled=1

gpgcheck=1

gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key

exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni

[vagrant@mero ~]$ sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

sudo kubeadm init --control-plane-endpoint=master01.mero.org --pod-network-cidr=10.244.0.0/16

[init] Using Kubernetes version: v1.30.3

[preflight] Running pre-flight checks

[WARNING Service-Kubelet]: kubelet service is not enabled, please run 'systemctl enable kubelet.service'

[preflight] Pulling images required for setting up a Kubernetes cluster

[preflight] This might take a minute or two, depending on the speed of your internet connection

[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'

W0724 06:31:29.968495    9394 checks.go:844] detected that the sandbox image "registry.k8s.io/pause:3.8" of the container runtime is inconsistent with that used by kubeadm.It is recommended to use "registry.k8s.io/pause:3.9" as the CRI sandbox image.

[certs] Using certificateDir folder "/etc/kubernetes/pki"

[certs] Generating "ca" certificate and key

[certs] Generating "apiserver" certificate and key

[certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local master01.mero.org] and IPs [10.96.0.1 10.0.2.15]

[certs] Generating "apiserver-kubelet-client" certificate and key

[certs] Generating "front-proxy-ca" certificate and key

[certs] Generating "front-proxy-client" certificate and key

[certs] Generating "etcd/ca" certificate and key

[certs] Generating "etcd/server" certificate and key

[certs] etcd/server serving cert is signed for DNS names [localhost master01.mero.org] and IPs [10.0.2.15 127.0.0.1 ::1]

[certs] Generating "etcd/peer" certificate and key

[certs] etcd/peer serving cert is signed for DNS names [localhost master01.mero.org] and IPs [10.0.2.15 127.0.0.1 ::1]

[certs] Generating "etcd/healthcheck-client" certificate and key

[certs] Generating "apiserver-etcd-client" certificate and key

[certs] Generating "sa" key and public key

[kubeconfig] Using kubeconfig folder "/etc/kubernetes"

[kubeconfig] Writing "admin.conf" kubeconfig file

[kubeconfig] Writing "super-admin.conf" kubeconfig file

[kubeconfig] Writing "kubelet.conf" kubeconfig file

[kubeconfig] Writing "controller-manager.conf" kubeconfig file

[kubeconfig] Writing "scheduler.conf" kubeconfig file

[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"

[control-plane] Using manifest folder "/etc/kubernetes/manifests"

[control-plane] Creating static Pod manifest for "kube-apiserver"

[control-plane] Creating static Pod manifest for "kube-controller-manager"

[control-plane] Creating static Pod manifest for "kube-scheduler"

[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"

[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"

[kubelet-start] Starting the kubelet

[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests"

[kubelet-check] Waiting for a healthy kubelet. This can take up to 4m0s

[kubelet-check] The kubelet is healthy after 1.504085378s

[api-check] Waiting for a healthy API server. This can take up to 4m0s

[api-check] The API server is healthy after 7.503268417s

[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace

[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster

[upload-certs] Skipping phase. Please see --upload-certs

[mark-control-plane] Marking the node master01.mero.org as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]

[mark-control-plane] Marking the node master01.mero.org as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]

[bootstrap-token] Using token: k1wgwc.l4lri93tjgajnrm4

[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles

[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes

[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials

[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token

[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster

[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace

[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key

[addons] Applied essential addon: CoreDNS

[addons] Applied essential addon: kube-proxy

  

Your Kubernetes control-plane has initialized successfully!

  

To start using your cluster, you need to run the following as a regular user:

  

  mkdir -p $HOME/.kube

  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

  sudo chown $(id -u):$(id -g) $HOME/.kube/config

  

Alternatively, if you are the root user, you can run:

  

  export KUBECONFIG=/etc/kubernetes/admin.conf

  

You should now deploy a pod network to the cluster.

Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:

  https://kubernetes.io/docs/concepts/cluster-administration/addons/

  

You can now join any number of control-plane nodes by copying certificate authorities

and service account keys on each node and then running the following as root:

  

  kubeadm join master01.mero.org:6443 --token k1wgwc.l4lri93tjgajnrm4 \

--discovery-token-ca-cert-hash sha256:f0510da11824c12090d8d57d180ac84d4064e64b5eea407ff01b20b0f322981b \

--control-plane 

  

Then you can join any number of worker nodes by running the following on each as root:

  

kubeadm join master01.mero.org:6443 --token k1wgwc.l4lri93tjgajnrm4 \

--discovery-token-ca-cert-hash sha256:f0510da11824c12090d8d57d180ac84d4064e64b5eea407ff01b20b0f322981b 

[vagrant@mero ~]$  mkdir -p $HOME/.kube

  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

  sudo chown $(id -u):$(id -g) $HOME/.kube/config

cp: overwrite '/home/vagrant/.kube/config'? y

[vagrant@mero ~]$ kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml

namespace/kube-flannel created

serviceaccount/flannel created

clusterrole.rbac.authorization.k8s.io/flannel created

clusterrolebinding.rbac.authorization.k8s.io/flannel created

configmap/kube-flannel-cfg created

daemonset.apps/kube-flannel-ds created
```

