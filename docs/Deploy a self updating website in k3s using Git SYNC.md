[Folder with all yaml files](/Users/manxe/Desktop/Work/rocky_k3s/rocky_k3s/using-git-sync)
```deployment.yaml
apiVersion: apps/v1

kind: Deployment

metadata:

  name: nginx-deploy-choco

  labels:

    app: nginx-cc

spec:

  replicas: 1

  selector:

    matchLabels:

      app: nginx-cc

  template:

    metadata:

      labels:

        app: nginx-cc

    spec:

      containers:

        - name: git-sync

          image: registry.k8s.io/git-sync/git-sync:v4.2.3

          volumeMounts:

            - name: www-data

              mountPath: /data

          env:

            - name: GITSYNC_REPO

              value: "http://192.168.56.8:3000/rocky/chochoc.git"

            - name: GIT_SYNC_BRANCH

              value: "main"

            - name: GITSYNC_ROOT

              value: /data

            - name: GIT_SYNC_DEST

              value: "html"

            - name: GITSYNC_ONE_TIME

              value: "false"

            - name: GIT_SYNC_COMMAND_AFTER

              value: "nginx -s reload"

          securityContext:

            runAsUser: 0

        - name: nginx-choco

          image: nginx

          ports:

            - containerPort: 80

          volumeMounts:

            - mountPath: "/usr/share/nginx/"

              name: www-data

            - mountPath: "/etc/ssl/haitmeroa.tech.com.key"

              name: secret-configmap-volume

              subPath: haitmeroa.tech.com.key

            - mountPath: "/etc/ssl/haitmeroa.tech.com.crt"

              name: secret-configmap-volume

              subPath: haitmeroa.tech.com.crt

            - mountPath: "/etc/nginx/conf.d/default.conf"

              name: secret-configmap-volume

              subPath: haitmero.conf 

      volumes:

        - name: www-data

          emptyDir: {}

        - name: secret-configmap-volume

          configMap:

            name: secret-configmap
```

```service.yaml
apiVersion: v1

kind: Service

metadata:

  name: nginx-service

  labels:

    app: nginx-cc

spec:

  type: NodePort

  selector:

    app: nginx-cc 

  ports:

    - port: 80

      nodePort: 30080

      name: http

    - port: 443

      nodePort: 30443

      name: https
```


```ingress.yaml
apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

  name: site-nginx-ingress

  annotations:

    nginx.ingress.kubernetes.io/ssl-redirect: "false"

spec:

  rules:

  - host: haitmeroa.tech

    http:

      paths:

        - path: /

          pathType: Prefix

          backend:

            service:

              name: nginx-service

              port:

                number: 80
```


```nginx-configmap.yaml 

apiVersion: v1

kind: ConfigMap

metadata:

  name: secret-configmap

  labels:

    app: nginx-cc

data:

  haitmeroa.tech.com.key: |

      -----BEGIN PRIVATE KEY-----

      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

      SJx4wmU0sikn5W0j529BJzcQqm5WUEw=

      -----END PRIVATE KEY-----

  

  haitmeroa.tech.com.crt: |

      -----BEGIN CERTIFICATE-----

      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

      jkIMt5KLOVkvXoKeGg==

      -----END CERTIFICATE-----

  haitmero.conf: |

      server {

      listen         80;

      server_name haitmeroa.tech www.haitmeroa.tech;

      return 301 https://$server_name$request_uri;

      location / {

        root   /usr/share/nginx/html;

        index  index.html index.htm;

      }

      location = /50x.html {

        root   /usr/share/nginx/html;

       }

      }

      server {

      listen                    443 ssl;

      listen         [::]:443 ssl;

      server_name haitmeroa.tech www.haitmeroa.tech;

      ssl_certificate           /etc/ssl/haitmeroa.tech.com.crt;

      ssl_certificate_key       /etc/ssl/haitmeroa.tech.com.key;

      access_log /var/log/nginx/nginx.haitmeroa.tech.com.access.log;

      error_log         /var/log/nginx/nginx.haitmeroa.tech.com.error.log;

      location / {

        root   /usr/share/nginx/html;

        index  index.html index.htm;

        }

       location = /50x.html {

        root   /usr/share/nginx/html;

        }

      }
```

## The above config allow to deploy a auto updating app with TLS (using config maps)