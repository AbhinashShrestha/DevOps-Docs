```deploy.yaml 

---

apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:

  name: zon

  annotations:

    kubernetes.io/ingress.class: "traefik"

spec:

  rules:

  - host: "mero-zon.com"

    http:

      paths:

      - pathType: Prefix

        path: /

        backend:

          service:

            name: zon-service

            port:

              number: 80

---

apiVersion: v1

kind: Service

metadata:

  name: zon-service

spec:

  ports:

    - port: 80

      protocol: TCP

  selector:

    app: zon

  

---

apiVersion: apps/v1

kind: Deployment

metadata:

  name: zon-nginx

spec:

  selector:

    matchLabels:

      app: zon

  replicas: 1

  template:

    metadata:

      labels:

        app: zon

    spec:

      containers:

      - name: zon

        image: abhizeet/zon

        ports:

        - containerPort: 80
```
