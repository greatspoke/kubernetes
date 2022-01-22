## To start using certificate-and-ingress on K8s cluster

##### Now it's time to request the TLS certificate in your kubernetes cluster:
```
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: certificate
  namespace: production
spec:
  secretName: production-certificate-tls
  issuerRef:
    name: letsencrypt
  dnsNames:
    - "<your-domain>"
    - "*.<your-domain>"

```


##### Then we will use annotations on the ingress with ingress-shim, so our ingress conffiguration will look like the following:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: production-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    cert-manager.io/issuer: "letsencrypt"
spec:
  tls:
  - hosts:
    - <your-domain>
    - "*.<your-domain>"
    secretName: production-certificate-tls
  rules:
    - host: production.<your-domain>
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: your-production-service
                port:
                  number: 80
```


##### Check if your certificate have installed and works:
```
kubectl get certificate -n production
```

##### The result should be like this:
```
NAME                         READY   SECRET                       AGE
certificate                  True    production-certificate-tls   10S
production-certificate-tls   True    production-certificate-tls   10S

```

##### Then we need edit the deployment of your ingress-nginx-controller. Why? Because, if it doesn't find the secret in the namespace it's deployed in, the Nginx controller deploys it's own certificate (fake certificate):
```
kubectl edit ingress-nginx-controller -n production
```

##### And add this line to last arguments:
```
--default-ssl-certificate=production/production-certificate-tls
```


##### It should be like this:
```
containers:
- name: controller
    image: >-
    k8s.gcr.io/ingress-nginx/controller:v0.41.2@sha256:1f4f402b9c14g3ae92b11ada1dfe9893a88f0fasb0b2f4b903e2c67a0c3bf0de
    args:
    - /nginx-ingress-controller
    - '--publish-service=$(POD_NAMESPACE)/ingress-nginx-controller'
    - '--election-id=ingress-controller-leader'
    - '--ingress-class=nginx'
    - '--configmap=$(POD_NAMESPACE)/ingress-nginx-controller'
    - '--validating-webhook=:8443'
    - '--validating-webhook-certificate=/usr/local/certificates/cert'
    - '--validating-webhook-key=/usr/local/certificates/key'
    - '--default-ssl-certificate=production/production-certificate-tls'
```

## Done! Your certificate and Ingress installed successfully! 
### Now you can go to your link `production.<your-domain>` and check the results in browser.


