
### Terms to know
- ServiceAccount 
- UserAccount
- Hardware Security Modules (HSMs)
- Key Management Systems (KMSs)

## Procedure to set up Vault as secret manager
1. Install k3s
2. Install helm
3. Install vault
	Using Helm
```
helm install vault hashicorp/vault \
  --set 'server.dev.enabled=true' \
  --set 'ui.enabled=true' \
  --set 'ui.serviceType=LoadBalancer' \
  --set 'server.extraArgs="-dev-listen-address=0.0.0.0:8200"' \
  --namespace vault \
  --f values.yaml
  --dry-run
```

4. 
How to get inside the container for further config:

```
 kubectl exec -it --namespace=vault vault-0 -- /bin/sh
```

Go to vault ui
create secret 
path: hajimete-no-secret
username = watashi
password = watashi


Check if the config is correct 
```
kubectl exec -it --namespace=vault pod/vault-test-84655998cd-kczdq -c vault-agent-init -- /bin/sh
```

```
cat /etc/resolv.conf

search vault.svc.cluster.local svc.cluster.local cluster.local

nameserver 10.43.0.10

options ndots:5

/ $ vi /etc/resolv.conf
```


make it 

```
vault.vault.svc
```


To SEE LOGS OF A SPECIFIC CONTAINER

```
kubectl logs pod/vault-test-565d9fc788-zh77s -c vault-agent-init
```
