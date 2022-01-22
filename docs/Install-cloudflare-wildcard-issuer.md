## To start using cloudflare-wildcard-issuer on K8s cluster

##### First we need get cloudflare API token. Going to [cloudflare-api-token] and click Create token:
```
Using template `Edit zone DNS`
```



##### Add two edit permissions to apply to your accounts or websites for this token Permissions:
```
Zone.Zone, Zone.DNS with Resources group All zones
```

`Copy your new working token`


##### So, now time to install our secret and then cloudflare-wildcard-issuer:
```
apiVersion: v1
kind: Secret
metadata:
  name: cloudflare-api-token-secret
  namespace: production
type: Opaque
stringData:
  api-token: <your cloudflare api token here>
```

##### Install cloudflare-wildcard-issuer:
```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: letsencrypt
  namespace: production
spec:
  acme:
    email: <your email>
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-account-key
    solvers:
      - dns01:
          cloudflare:
            email: <your email>
            apiTokenSecretRef:
              name: cloudflare-api-token-secret
              key: api-token
```

##### Check if it`s work correctly:
```
kubectl get issuer -n production
```
#### You should get something like this:
```
NAME          READY   AGE
letsencrypt   True    10S
```



# For next step need to install [ingress-nginx-controller] 


[cloudflare-api-token]: https://dash.cloudflare.com/profile/api-tokens
[ingress-nginx-controller]: https://github.com/greatspoke/kubernetes/blob/master/docs/Install-ingress-nginx-controller.md