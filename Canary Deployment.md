Canary deployment in Kubernetes allows you to test new versions of your application with a small subset of users or traffic before rolling it out to the entire production environment. Here's how you can implement a basic canary deployment using Kubernetes and Nginx Ingress:

### Step-by-Step Guide

#### 1. **Create Canary and Production Deployments**

Create two separate deployments for your canary and production versions of the application. Here are the YAML definitions for both deployments:

**production/deployment.yaml:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: production
  namespace: learn-canary
  labels:
    app: production
spec:
  replicas: 1
  selector:
    matchLabels:
      app: production
  template:
    metadata:
      labels:
        app: production
    spec:
      containers:
      - name: production
        image: nginx  # Replace with your production image
        ports:
        - containerPort: 80
```

**canary/deployment.yaml:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: canary
  namespace: learn-canary
  labels:
    app: canary
spec:
  replicas: 1
  selector:
    matchLabels:
      app: canary
  template:
    metadata:
      labels:
        app: canary
    spec:
      containers:
      - name: canary
        image: abhizeet/zon  # Replace with your canary image
        ports:
        - containerPort: 80
```

Apply these deployment configurations:

```bash
kubectl apply -f production/deployment.yaml
kubectl apply -f canary/deployment.yaml
```

#### 2. **Create Services for Deployments**

Create Kubernetes Services for both canary and production deployments. These services will expose your deployments internally.

**production/service.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: production
  namespace: learn-canary
  labels:
    app: production
  annotations:
    metallb.universe.tf/loadBalancerIPs: 10.0.2.20  # Replace with your production IP
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    app: production
```

**canary/service.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: canary
  namespace: learn-canary
  labels:
    app: canary
  annotations:
    metallb.universe.tf/loadBalancerIPs: 10.0.2.21  # Replace with your canary IP
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    app: canary
```

Apply these service configurations:

```bash
kubectl apply -f production/service.yaml
kubectl apply -f canary/service.yaml
```

#### 3. **Create Ingress for Canary Deployment**

Create an Ingress resource that routes traffic between your canary and production deployments based on defined rules.

**ingress_canary.yaml:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: canary
  namespace: learn-canary
  annotations:
    kubernetes.io/ingress.class: nginx-internal
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "20"  # 20% traffic to canary
spec:
  ingressClassName: nginx
  rules:
  - host: echo.prod.mydomain.com  # Replace with your domain
    http:
      paths:
      - pathType: Prefix
        path: /
        backend:
          service:
            name: canary
            port:
              number: 80
      - pathType: ImplementationSpecific
```

Apply the Ingress configuration:

```bash
kubectl apply -f ingress_canary.yaml
```

#### 4. **Verify and Monitor**

- **Verify Deployments:** Ensure that both canary and production deployments are running correctly:
  ```bash
  kubectl get deployments -n learn-canary
  ```

- **Verify Services:** Check that services are correctly exposing the deployments:
  ```bash
  kubectl get services -n learn-canary
  ```

- **Verify Ingress:** Confirm that the Ingress rules are applied correctly:
  ```bash
  kubectl get ingresses -n learn-canary
  ```

#### 5. **Testing Canary Deployment**

Test the canary deployment by accessing your application through the configured domain (e.g., `echo.prod.mydomain.com`). Adjust the traffic splitting ratio (`nginx.ingress.kubernetes.io/canary-weight`) as needed.
```
for i in $(seq 1 10); do curl -v -s --resolve echo.prod.mydomain.com:80:10.0.2.19 echo.prod.mydomain.com; don
  
for i in $(seq 1 10); do curl -s --resolve echo.prod.mydomain.com:80:10.0.2.19 echo.prod.mydomain.com | grep -o '<h[13]>.*</h[13]>'; done
```

#### 6. **Monitoring and Metrics**

Use Kubernetes tools like metrics-server and Prometheus to monitor resource usage and application performance during the canary deployment. Analyze metrics such as CPU utilization, memory usage, and response times to determine the impact of the new version.

By following these steps, you can implement a basic canary deployment pattern in Kubernetes using Nginx Ingress for traffic splitting between canary and production versions of your application. Adjust the configurations and metrics according to your specific use case and requirements.


[Reference]([https://stackoverflow.com/questions/57929675/kubernetes-ingress-controller-returning-503-service-unavailable](https://stackoverflow.com/questions/57929675/kubernetes-ingress-controller-returning-503-service-unavailable))
