 Set up a K3s pod to serve static content and use a separate Nginx server to cache and reverse proxy.

## Nginx Configuration on Host

1. **Edit the Nginx Configuration File**
    ```bash
    sudo vim /etc/nginx/conf.d/meroapp.conf
    ```

2. **Nginx Configuration Test**
    ```bash
    sudo nginx -t
    # Output:
    # nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    # nginx: configuration file /etc/nginx/nginx.conf test is successful
    ```

3. **Reload Nginx Configuration**
    ```bash
    sudo nginx -s reload
    # Output:
    # nginx: [error] invalid PID number "" in "/run/nginx.pid"
    ```

4. **Restart Nginx Service**
    ```bash
    sudo systemctl restart nginx
    ```

5. **Test Nginx Configuration**
    ```bash
    curl http://nginx-host-sys.com:81
    # Expected Output:
    # It's me mario okokok
    ```

## Kubernetes (K3s) Deployment

1. **View Kubernetes Resources**
    ```bash
    kubectl get all
    # Example Output:
    # NAME                                        READY   STATUS    RESTARTS   AGE
    # pod/nginx-k3s-deployment-7c666b779c-k9znc   1/1     Running   0          2m16s
    #
    # NAME                    TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
    # service/kubernetes      ClusterIP      10.43.0.1     <none>        443/TCP        25h
    # service/nginx-service   LoadBalancer   10.43.8.123   10.0.2.15     80:30976/TCP   2m16s
    #
    # NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
    # deployment.apps/nginx-k3s-deployment   1/1     1            1           2m16s
    #
    # NAME                                              DESIRED   CURRENT   READY   AGE
    # replicaset.apps/nginx-k3s-deployment-7c666b779c   1         1         1       2m16s
    ```

2. **Deployment Configuration**
    - `app.yaml`
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-k3s-deployment
      labels:
        app: nginx-app
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx-app
      template:
        metadata:
          labels:
            app: nginx-app
        spec:
          containers:
            - name: nginx-app
              image: nginx:latest
              ports:
                - containerPort: 80
              volumeMounts:
                - mountPath: "/etc/nginx/conf.d/default.conf"
                  name: nginx-configmap-volume
                  subPath: meroapp.conf 
          volumes:
            - name: nginx-configmap-volume
              configMap:
                name: nginx-configmap
    ```

3. **Service Configuration**
    - `service.yaml`
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-service
      labels:
        app: nginx-app
    spec:
      type: LoadBalancer
      ports:
        - port: 80
          targetPort: 80
      selector:
        app: nginx-app
    ```

4. **ConfigMap Configuration**
    - `nginx-config.yaml`
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: nginx-configmap
      labels:
        app: nginx-app
    data:
      meroapp.conf: |
        server {
          listen 80;
          server_name tsuma.com www.tsuma.com;

          location / {
              proxy_pass http://192.168.56.61:3000/devops/bucker/raw/branch/main/a.txt;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
          }

          location /image {
              proxy_pass http://192.168.56.61:3000/devops/bucker/raw/branch/main/images/dancer.jpg;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
          }
        }
    ```

## Nginx Configuration for Caching and Reverse Proxy

1. **Host Nginx Configuration**
    - `/etc/nginx/conf.d/meroapp.conf`

```/etc/nginx/conf.d/meroapp.conf 
proxy_cache_path  /var/cache/nginx/meroapp  keys_zone=merocache:10m levels=1:2 inactive=2m max_size=100m use_temp_path=off;

proxy_cache_key "$scheme$request_method$host$request_uri";

proxy_cache_valid 200 302 5m;

server {
    listen       81;
    server_name  192.168.56.61 nginx-host-sys.com;
    access_log   /var/log/nginx/nginx-host-sys.com/access.log;
    error_log    /var/log/nginx/nginx-host-sys.com/error.log;
    location / {
        proxy_cache             merocache;
        proxy_set_header        Range $slice_range;
        proxy_cache_valid       any 2m;
        add_header              X-Cache-Status $upstream_cache_status;
        proxy_buffering on;
        proxy_ignore_headers Expires;
        proxy_ignore_headers X-Accel-Expires;
        proxy_ignore_headers Cache-Control;
        proxy_ignore_headers Set-Cookie;
        proxy_hide_header X-Accel-Expires;
        proxy_hide_header Expires;
        proxy_hide_header Cache-Control;
        proxy_hide_header Pragma;
        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header        X-Forwarded-Proto $scheme;
        proxy_pass              http://10.0.2.16:80;
    }
}
```

### Commands
- Edit Nginx Configuration:
    ```bash
    sudo vim /etc/nginx/conf.d/meroapp.conf
    ```

- Test Nginx Configuration:
    ```bash
    sudo nginx -t
    ```

- Reload Nginx:
    ```bash
    sudo nginx -s reload
    ```

- Restart Nginx:
    ```bash
    sudo systemctl restart nginx
    ```

- Test Nginx Server:
    ```bash
    curl http://nginx-host-sys.com:81
    ```

- View Kubernetes Resources:
    ```bash
    kubectl get all
    ```

This setup ensures your static content is served efficiently with caching and reverse proxy through Nginx.