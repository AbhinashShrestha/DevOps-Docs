1. Apply the appropriate manifest
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.5/config/manifests/metallb-native.yaml
```
2. Create a separate directory
		mkdir metallb
3. Create pool and add yaml
```pool.yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 10.0.2.15-10.0.2.50 (This should be according to your ip allocation)
```

```add.yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: example
  namespace: metallb-system
```

[MAIN DOC](https://metallb.universe.tf/configuration/)
