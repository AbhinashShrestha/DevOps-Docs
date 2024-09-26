```app.yml
apiVersion: networking.k8s.io/v1
#apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myapp-ingress
  namespace: web
  annotations:
    kubernetes.io/ingress.class: traefik
spec:
  tls:
    - secretName: mero-secret
      hosts:
        - "ajakodin.com"
  rules:
    - host: ajakodin.com
      http:
        paths:
          - path: /
            pathType: Prefix
              backend:
                service:
                  name: myapp-service
                    port:
                      number: 3000
  
apiVersion: networking.k8s.io/v1
#apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: myapp-ingress
  namespace: web
  annotations:
    kubernetes.io/ingress.class: traefik
spec:
  tls:
    - secretName: mero-secret
      hosts:
        - "ajakodin.com"
  rules:
    - host: ajakodin.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              serviceName: myapp-service
              servicePort: *3000*
```