## To start using docker-registry on your kubernetes clusster

##### First we need create container:
```
kubectl create ns registry
```
The next step you need to create an application in the namespace registry
##### You can use helm repositories bitnami:
```
helm install stable/docker-registry
```
##### Deploy an issuer:
```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt-outside
  namespace: registry
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: <"your email">
    privateKeySecretRef:
      name: letsencrypt-outside
    solvers:
      - http01:
          ingress:
            class: nginx

```
##### Get the issuer:
```
kubectl get issuer -n registry
NAME                  READY   AGE
letsencrypt-outside   True    1m

```
##### Then deploy a certificate:
```
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: registry-tls
  namespace: registry
spec:
  secretName: registry-tls
  issuerRef:
    name: letsencrypt-outside
  dnsNames:
    - <"registry.yourdomnain.com">

```
##### We need to wait when certificate will show status true. Time around is 1-2 minutes
```
kubectl get certificate -n registry
NAME           READY   SECRET         AGE
registry-tls   True    registry-tls   2m
```
##### The last step - we need deploy an ingress:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: registry
  namespace: registry
  annotations:
    kubernetes.io/ingress.class: "nginx"
    kubernetes.io/tls-acme: "true"
    cert-manager.io/cluster-issuer: "letsencrypt-outside"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
spec:
  tls:
    - hosts:
        - registry.yourdomain.com
      secretName: registry-tls
  rules:
    - host: registry.yourdomain.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: docker-registry-1643965588
                port:
                  number: 5000
```

## Perhaps you wondered how to secure your registry? That's right, you need to install a security system! Let me show you how to do it
##### Thats easy way to do it:
```
htpasswd -c auth registry
kubectl create secret generic registry-auth --from-file=auth -n registry
kubectl get secret registry-auth -o yaml -n registry
```
##### And apply ingress.yaml with new lines of code:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: registry
  namespace: registry
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: registry-auth
    nginx.ingress.kubernetes.io/auth-realm: "Authentication Required - registry"
spec:
  tls:
    - hosts:
        - registry.yourdomain.com
      secretName: registry-tls
  rules:
    - host: registry.yourdomain.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: docker-registry-1643965588
                port:
                  number: 5000
```

Thats all! `Happy Kuberneting`!!


