## [Main](https://developer.hashicorp.com/vault/tutorials/kubernetes/kubernetes-external-vault) [alt](https://developer.hashicorp.com/vault/tutorials/kubernetes/kubernetes-external-vault) [ref](https://developer.hashicorp.com/vault/tutorials/integrate-kubernetes-hcp-vault-dedicated/kubernetes-hcp-vault) [ref](https://developer.hashicorp.com/vault/tutorials/kubernetes/agent-kubernetes)
[Production Hardening](https://developer.hashicorp.com/vault/tutorials/operations/production-hardening)

1. Installation
```
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install vault
```

You will see: 
```
Generating Vault TLS key and self-signed certificate...

.+.+......+...+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.........+...+....+...+...+..+.......+.....+...+....+......+........+.....................+..........+......+........+.+.....+.+.....+............+..........+......+.....+.+...+........+.......+..+.+........+.........+...+...+....+..+.+..+......+.......+...+.....+.......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..........+......+......................+...+........................+...........+....+...+..+.......+.....+....+.....+.+..................+...+..............+.+.....+.........+......+....+........+......+......+.....................+.......+.....+......+.......+...............+...+...........+....+..+...+.........+...........................+...+.+..+.........+...............+.+...+.........+......+................................+...+.........+.......+........+.+...........+......+.........+....+..+..........+...+...........+...+...+...+.+.........+..+.............+.....+..........+......+......+...............+.....+...+.........+..........+............+...+...+.................+............+.+........+................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+......+.........+...+............+...+..+.............+..+.+............+..+.........+..........+.....+.+...+...+..+......+......+.+...+..+.......+...+..+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.................................+.....+.........+............+.+..+.......+...........+..........+.....+....+...+.....+..........+...+........+.+.....+.+...+......+.....+....+.....+....+..+.........+.......+...+............+.................+.+..+....+...+.....+..........+...........+.+........+......+.+...+.....+......+...................+..+.+...+..+.............+.....+............+...+....+..+...................+.................+.......+..+.........................+.......................+......+.........+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

-----

Vault TLS key and self-signed certificate have been generated in '/opt/vault/tls'.

  

  Verifying        : vault-1.17.2-1.x86_64                                                                                                                                                              1/1 

  

Installed:

  vault-1.17.2-1.x86_64
```

Then 
```
vault server -dev -dev-root-token-id root -dev-listen-address 0.0.0.0:8200
```


Gives the output 
```
WARNING! dev mode is enabled! In this mode, Vault runs entirely in-memory

and starts unsealed with a single unseal key. The root token is already

authenticated to the CLI, so you can immediately begin using Vault.

  

You may need to set the following environment variables:

  

    $ export VAULT_ADDR='http://127.0.0.1:8200'

  

The unseal key and root token are displayed below in case you want to

seal/unseal the Vault or re-authenticate.

  

Unseal Key: ZOgR5SXmUkiCpjT19hePmbqpHViucIjScDWBEIiDEUY=

Root Token: hvs.Wu8hVo25iKwxuciEH7gxHTRR

  

Development mode should NOT be used in production installations!
```


Now export 
```
vim ~/.bashrc
export VAULT_ADDR='http://0.0.0.0:8200'
export VAULT_TOKEN="root"
source ~/.bashrc
vault status

Key             Value

---             -----

Seal Type       shamir

Initialized     true

Sealed          false

Total Shares    1

Threshold       1

Version         1.17.2

Build Date      2024-07-05T15:19:12Z

Storage Type    inmem

Cluster Name    vault-cluster-c1afed62

Cluster ID      95cfea8d-f970-0824-3b81-569733341fd4

HA Enabled      false
```

k3s pod Retrieve secrets from vault server

```
apiVersion: v1

kind: Pod

metadata:

  name: devwebapp

  labels:

    app: devwebapp

spec:

  serviceAccountName: internal-app

  containers:

    - name: app

      image: burtlo/devwebapp-ruby:k8s

      env:

      - name: VAULT_ADDR

        value: "http://192.168.56.60:8200"

      - name: VAULT_TOKEN

        value: root

~
```


Create external-vault.yaml to allow hiding of ip and use k3s native pods
## Deploy service and endpoints to address an external Vault

```external-vault.yaml
---
apiVersion: v1
kind: Service
metadata:
  name: external-vault
  namespace: default
spec:
  ports:
  - protocol: TCP
    port: 8200
---
apiVersion: v1
kind: Endpoints
metadata:
  name: external-vault
subsets:
  - addresses:
      - ip: 192.168.56.60
    ports:
      - port: 8200
```


### K3s deployment pod that will retrieve secrets injected by vault injector 

```app-with-annotations.yaml 

apiVersion: v1

kind: Pod

metadata:

  name: devwebapp-with-annotations

  labels:

    app: devwebapp-with-annotations

  annotations:

    vault.hashicorp.com/agent-inject: 'true'

    vault.hashicorp.com/role: 'mero'

    vault.hashicorp.com/agent-inject-secret-credentials.txt: 'secret/data/mero/config'

spec:

  serviceAccountName: internal-app

  containers:

    - name: app

      image: burtlo/devwebapp-ruby:k8s
```


## Install the Vault Helm chart configured to address an external Vault

1. Add the HashiCorp Helm repository
```
helm repo add hashicorp https://helm.releases.hashicorp.com
```
2. Update all the repositories to ensure `helm` is aware of the latest versions
```
helm repo update
```
3. Install the latest version of the Vault server running in external mode
```
helm install vault hashicorp/vault \
    --set "global.externalVaultAddr=http://external-vault:8200"
```

```
[vagrant@ha ~]$ kubectl apply -f devwebapp.yaml 

pod/devwebapp created

[vagrant@ha ~]$ kubectl exec devwebapp -- curl -s localhost:8080 ; echo

{"password"=>"salsa", "username"=>"giraffe"}

[vagrant@ha ~]$ kubectl apply --filename external-vault.yaml

service/external-vault created

endpoints/external-vault created

[vagrant@ha ~]$ kubectl exec devwebapp -- curl -s http://external-vault:8200/v1/sys/seal-status | jq

**{**

  **"type"****:** "shamir"**,**

  **"initialized"****:** true**,**

  **"sealed"****:** false**,**

  **"t"****:** 1**,**

  **"n"****:** 1**,**

  **"progress"****:** 0**,**

  **"nonce"****:** ""**,**

  **"version"****:** "1.17.2"**,**

  **"build_date"****:** "2024-07-05T15:19:12Z"**,**

  **"migration"****:** false**,**

  **"cluster_name"****:** "vault-cluster-b65c90fe"**,**

  **"cluster_id"****:** "f09b3432-7576-890c-33f3-04c712590a26"**,**

  **"recovery_seal"****:** false**,**

  **"storage_type"****:** "inmem"

**}**

[vagrant@ha ~]$ vault status

Key             Value

---             -----

Seal Type       shamir

Initialized     true

Sealed          false

Total Shares    1

Threshold       1

Version         1.17.2

Build Date      2024-07-05T15:19:12Z

Storage Type    inmem

Cluster Name    vault-cluster-b65c90fe

Cluster ID      f09b3432-7576-890c-33f3-04c712590a26

HA Enabled      false

[vagrant@ha ~]$ kubectl apply --filename=pod-devwebapp-through-service.yaml

error: the path "pod-devwebapp-through-service.yaml" does not exist

[vagrant@ha ~]$ ls

devwebapp.yaml  external-vault.yaml  **get_helm.sh**  pod-devwebapp-with-annotations.yaml  **vault-debug-2024-07-16T07-23-02Z.tar.gz**

[vagrant@ha ~]$ vim devwebapp-through-service.yaml

[vagrant@ha ~]$ vim devwebapp-with-annotations.yaml

[vagrant@ha ~]$ vim devwebapp-with-annotations.yaml

[vagrant@ha ~]$ kubectl apply -f devwebapp-with-annotations.yaml 

pod/devwebapp-with-annotations created

[vagrant@ha ~]$ kubectl exec devwebapp-with-annotations -- curl -s localhost:8080 ; echo

{"password"=>"salsa", "username"=>"giraffe"}

[vagrant@ha ~]$ vim devwebapp-with-annotations.yaml

[vagrant@ha ~]$ kubectl exec devwebapp-with-annotations -- curl -s localhost:8080 ; echo

{"password"=>"salsa", "username"=>"giraffe"}

[vagrant@ha ~]$ helm repo add hashicorp https://helm.releases.hashicorp.com

"hashicorp" has been added to your repositories

[vagrant@ha ~]$ helm repo update

Hang tight while we grab the latest from your chart repositories...

...Successfully got an update from the "hashicorp" chart repository

Update Complete. ⎈Happy Helming!⎈

[vagrant@ha ~]$ helm install vault hashicorp/vault \

    --set "global.externalVaultAddr=http://external-vault:8200"

NAME: vault

LAST DEPLOYED: Tue Jul 16 09:21:59 2024

NAMESPACE: default

STATUS: deployed

REVISION: 1

TEST SUITE: None

NOTES:

Thank you for installing HashiCorp Vault!

  

Now that you have deployed Vault, you should look over the docs on using

Vault with Kubernetes available here:

  

https://developer.hashicorp.com/vault/docs

  

  

Your release is named vault. To learn more about the release, try:

  

  $ helm status vault

  $ helm get manifest vault

[vagrant@ha ~]$ kubectl get pods

NAME                                    READY   STATUS              RESTARTS   AGE

devwebapp                               1/1     Running             0          16m

devwebapp-with-annotations              1/1     Running             0          7m

vault-agent-injector-84f94b7548-snwnd   0/1     ContainerCreating   0          13s

[vagrant@ha ~]$ kubectl get pods

NAME                                    READY   STATUS    RESTARTS   AGE

devwebapp                               1/1     Running   0          17m

devwebapp-with-annotations              1/1     Running   0          7m53s

vault-agent-injector-84f94b7548-snwnd   1/1     Running   0          66s

[vagrant@ha ~]$ kubectl describe serviceaccount vault

Name:                vault

Namespace:           default

Labels:              app.kubernetes.io/instance=vault

                     app.kubernetes.io/managed-by=Helm

                     app.kubernetes.io/name=vault

                     helm.sh/chart=vault-0.28.1

Annotations:         meta.helm.sh/release-name: vault

                     meta.helm.sh/release-namespace: default

Image pull secrets:  <none>

Mountable secrets:   <none>

Tokens:              <none>

Events:              <none>

[vagrant@ha ~]$ cat > vault-secret.yaml <<EOF

apiVersion: v1

kind: Secret

metadata:

  name: vault-token-g955r

  annotations:

    kubernetes.io/service-account.name: vault

type: kubernetes.io/service-account-token

EOF

[vagrant@ha ~]$ kubectl apply -f vault-secret.yaml

secret/vault-token-g955r created

[vagrant@ha ~]$ VAULT_HELM_SECRET_NAME=$(kubectl get secrets --output=json | jq -r '.items[].metadata | select(.name|startswith("vault-token-")).name')

[vagrant@ha ~]$ kubectl describe secret $VAULT_HELM_SECRET_NAME

Name:         vault-token-g955r

Namespace:    default

Labels:       <none>

Annotations:  kubernetes.io/service-account.name: vault

              kubernetes.io/service-account.uid: 033cd421-d285-408c-8856-b8a5ba03f4c8

  

Type:  kubernetes.io/service-account-token

  

Data

====

token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Imw4NXp5Rjh5STVOSHdVZklFQ3BlUU5uV2pnZnZzTU5MVzBZNEZCUUZjeFkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6InZhdWx0LXRva2VuLWc5NTVyIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6InZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMDMzY2Q0MjEtZDI4NS00MDhjLTg4NTYtYjhhNWJhMDNmNGM4Iiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OmRlZmF1bHQ6dmF1bHQifQ.I5IZqfkoNru-asjoAtJhZvwz7G0FLYd_yP71_oIQMGS1Bo7ZzoC28DCvPHbwDvTZnETFjGJ-3TRD5XL1nj8FavvNR4mGBE2TqczBJuRbHLErxcJLTCr6fk2BbJB_tf4Rq9YMaAtt3mZCgrNn8FcA0MBYeKN-9KQieO5plGbojc281tMHBRPFNroEq1nqcCN90i2FagC6ZrJeDK1rDDNnsX9nsJMXeGzPg6mL7aDnz2AkqUR52CPtRc2waNMK0lbnL6XfiwU-QOI3fu_0eg87DHoc8WbZDseffOvd5H_G4flvuG41NhURVaghhbM-Pq0oh8u_bzp2QD8GGlr7lrC01w

ca.crt:     566 bytes

namespace:  7 bytes

[vagrant@ha ~]$ kubectl describe serviceaccount vault

Name:                vault

Namespace:           default

Labels:              app.kubernetes.io/instance=vault

                     app.kubernetes.io/managed-by=Helm

                     app.kubernetes.io/name=vault

                     helm.sh/chart=vault-0.28.1

Annotations:         meta.helm.sh/release-name: vault

                     meta.helm.sh/release-namespace: default

Image pull secrets:  <none>

Mountable secrets:   <none>

Tokens:              vault-token-g955r

Events:              <none>

[vagrant@ha ~]$ kubectl describe serviceaccount vault

Name:                vault

Namespace:           default

Labels:              app.kubernetes.io/instance=vault

                     app.kubernetes.io/managed-by=Helm

                     app.kubernetes.io/name=vault

                     helm.sh/chart=vault-0.28.1

Annotations:         meta.helm.sh/release-name: vault

                     meta.helm.sh/release-namespace: default

Image pull secrets:  <none>

Mountable secrets:   <none>

Tokens:              vault-token-g955r

Events:              <none>

[vagrant@ha ~]$ kubectl get sa

NAME                   SECRETS   AGE

default                0         162m

internal-app           0         107m

vault                  0         2m55s

vault-agent-injector   0         2m55s

[vagrant@ha ~]$ kubectl describe serviceaccount vault

Name:                vault

Namespace:           default

Labels:              app.kubernetes.io/instance=vault

                     app.kubernetes.io/managed-by=Helm

                     app.kubernetes.io/name=vault

                     helm.sh/chart=vault-0.28.1

Annotations:         meta.helm.sh/release-name: vault

                     meta.helm.sh/release-namespace: default

Image pull secrets:  <none>

Mountable secrets:   <none>

Tokens:              vault-token-g955r

Events:              <none>

[vagrant@ha ~]$ vault auth enable kubernetes

Success! Enabled kubernetes auth method at: kubernetes/

[vagrant@ha ~]$ TOKEN_REVIEW_JWT=$(kubectl get secret $VAULT_HELM_SECRET_NAME --output='go-template={{ .data.token }}' | base64 --decode)

[vagrant@ha ~]$ KUBE_CA_CERT=$(kubectl config view --raw --minify --flatten --output='jsonpath={.clusters[].cluster.certificate-authority-data}' | base64 --decode)

[vagrant@ha ~]$ KUBE_HOST=$(kubectl config view --raw --minify --flatten --output='jsonpath={.clusters[].cluster.server}')

[vagrant@ha ~]$ vault write auth/kubernetes/config \

     token_reviewer_jwt="$TOKEN_REVIEW_JWT" \

     kubernetes_host="$KUBE_HOST" \

     kubernetes_ca_cert="$KUBE_CA_CERT" \

     issuer="https://kubernetes.default.svc.cluster.local"

Success! Data written to: auth/kubernetes/config

[vagrant@ha ~]$ vault policy write mero - <<EOF

path "secret/data/mero/config" {

  capabilities = ["read"]

}

EOF

Success! Uploaded policy: mero

[vagrant@ha ~]$ vault write auth/kubernetes/role/mero \

     bound_service_account_names=internal-app \

     bound_service_account_namespaces=default \

     policies=mero \

     ttl=24h

Success! Data written to: auth/kubernetes/role/mero

[vagrant@ha ~]$ vim pod-devwebapp-with-annotations.yaml 

[vagrant@ha ~]$ kubectl apply --filename pod-devwebapp-with-annotations.yaml

pod/devwebapp-with-annotations unchanged

[vagrant@ha ~]$ kubectl get pods

NAME                                    READY   STATUS    RESTARTS   AGE

devwebapp                               1/1     Running   0          24m

devwebapp-with-annotations              1/1     Running   0          14m

vault-agent-injector-84f94b7548-snwnd   1/1     Running   0          8m1s

[vagrant@ha ~]$ kubectl delete -f devwebapp-

devwebapp-through-service.yaml   devwebapp-with-annotations.yaml  

[vagrant@ha ~]$ kubectl delete -f devwebapp-with-annotations.yaml 

pod "devwebapp-with-annotations" deleted

[vagrant@ha ~]$ kubectl apply --filename pod-devwebapp-with-annotations.yaml

pod/devwebapp-with-annotations created

[vagrant@ha ~]$ kubectl exec -it devwebapp-with-annotations -c app -- cat /vault/secrets/credentials.txt

error: unable to upgrade connection: container not found ("app")

[vagrant@ha ~]$ vim devwebapp-with-annotations.yaml 

[vagrant@ha ~]$ kubectl get all

NAME                                        READY   STATUS    RESTARTS   AGE

pod/devwebapp                               1/1     Running   0          27m

pod/devwebapp-with-annotations              2/2     Running   0          107s

pod/vault-agent-injector-84f94b7548-snwnd   1/1     Running   0          10m

  

NAME                               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)    AGE

service/external-vault             ClusterIP   10.43.15.51   <none>        8200/TCP   26m

service/kubernetes                 ClusterIP   10.43.0.1     <none>        443/TCP    170m

service/vault-agent-injector-svc   ClusterIP   10.43.74.59   <none>        443/TCP    10m

  

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE

deployment.apps/vault-agent-injector   1/1     1            1           10m

  

NAME                                              DESIRED   CURRENT   READY   AGE

replicaset.apps/vault-agent-injector-84f94b7548   1         1         1       10m

[vagrant@ha ~]$ kubectl exec -it devwebapp-with-annotations -c app -- cat /vault/secrets/credentials.txt

data: map[password:watashi username:watashi]

metadata: map[created_time:2024-07-16T09:13:52.547790923Z custom_metadata:<nil> deletion_time: destroyed:false version:1]

[vagrant@ha ~]$ vim external-vault.yaml 

[vagrant@ha ~]$
```

[repo with stuff](https://github.com/hashicorp-education/learn-vault-external-kubernetes)
