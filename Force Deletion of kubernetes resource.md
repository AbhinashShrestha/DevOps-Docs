
```
# Delete all resources in all namespaces
kubectl delete all --all --all-namespaces

# Delete config maps, secrets, and other non-"all" resources
kubectl delete configmaps --all --all-namespaces
kubectl delete secrets --all --all-namespaces
kubectl delete pvc --all --all-namespaces
kubectl delete pv --all --all-namespaces
kubectl delete daemonsets --all --all-namespaces
kubectl delete deployments --all --all-namespaces
kubectl delete replicasets --all --all-namespaces
kubectl delete statefulsets --all --all-namespaces
kubectl delete jobs --all --all-namespaces
kubectl delete cronjobs --all --all-namespaces
kubectl delete serviceaccounts --all --all-namespaces
# Delete cluster roles and bindings
kubectl delete clusterroles --all
kubectl delete clusterrolebindings --all

# Delete storage classes
kubectl delete storageclasses --all

# Delete custom resource definitions (CRDs)
kubectl delete crds --all

```


https://kubernetes.io/blog/2021/05/14/using-finalizers-to-control-deletion/
  

cat <<EOF | curl -X PUT \

  localhost:8001/api/v1/namespaces/mero-ns/finalize \

  -H "Content-Type: application/json" \

  --data-binary @-

{

  "kind": "Namespace",

  "apiVersion": "v1",

  "metadata": {

    "name": “mero-ns”,

    "finalizers": []

  },

  "spec": {}

}

EOF

  

https://stackoverflow.com/questions/52954174/kubernetes-namespaces-stuck-in-terminating-status

kubectl delete all,ingress --all -n mero-ns

  

```
NAMESPACE= ; kubectl get namespace $NAMESPACE -o json | jq 'del(.spec.finalizers[0])' | kubectl replace --raw "/api/v1/namespaces/$NAMESPACE/finalize" -f -
```

  

kubectl delete all --all -n

  

kubectl delete pod NAME --grace-period=0 --force

and

kubectl delete pod NAME --now

  