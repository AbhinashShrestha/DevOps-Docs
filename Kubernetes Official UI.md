```
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
kubectl describe node <node_name> | grep Taints

kubectl taint node   <node_name>  node-role.kubernetes.io/control-plane:NoSchedule-
```