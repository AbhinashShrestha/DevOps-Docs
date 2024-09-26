
```
our Kubernetes control-plane has initialized successfully!

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

  kubeadm join alpha.mero.com:6443 --token s1w2g0.ybdtefkyy12rwbd7 \
	--discovery-token-ca-cert-hash sha256:16a5d82ef24c80e228a594804c41669465152e63d22113b5fb0e52e7bf38fae5 \
	--control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join alpha.mero.com:6443 --token s1w2g0.ybdtefkyy12rwbd7 \
	--discovery-token-ca-cert-hash sha256:16a5d82ef24c80e228a594804c41669465152e63d22113b5fb0e52e7bf38fae5 
```