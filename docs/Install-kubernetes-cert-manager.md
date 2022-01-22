## To start using cert-manager on K8s cluster

##### Setup role `master` on kubernetes.io node:
```
kubectl taint nodes kubectl node-role.kubernetes.io/master-
```
Install cert-manager using helm chart.

##### This time let's use helm chart to install our cert-manager:

```
helm repo add jetstack https://charts.jetstack.io
helm repo update
```
##### The following command will install cert-manager with custom resource definition(CRD):
```
helm install \
cert-manager jetstack/cert-manager \
--namespace cert-manager \
--create-namespace \
--version v1.6.1 \
--set installCRDs=true
```
### Make sure you set this value to true. installCRDs=true this is important beacuse it install the CRD.

##### Now let's make sure everything installed correct:
```
kubectl get pods -n cert-manager
```


##### You should see 3 pods similar like the following:
```
cert-manager-578486785-mgptg               1/1     Running   0          9h
cert-manager-cainjector-59676958d4-x2t9w   1/1     Running   3          9h
cert-manager-webhook-8d7495f4-88r5w        1/1     Running   0          9h
```


##### Now let's make sure as well our crd is there:
```
kubectl get crd | grep cert-manager
```


##### You should see the following crd:
```
certificaterequests.cert-manager.io              2021-07-07T05:59:38Z
certificates.cert-manager.io                     2021-07-07T05:59:40Z
challenges.acme.cert-manager.io                  2021-07-07T05:59:41Z
clusterissuers.cert-manager.io                   2021-07-07T05:59:42Z
issuers.cert-manager.io                          2021-07-07T05:59:43Z
orders.acme.cert-manager.io                      2021-07-07T05:59:44Z
```



`Now everything is installed correctly let's move to the next step!`

# For next step need to install [wildcard-issuer] 

[wildcard-issuer]: https://github.com/greatspoke/kubernetes/blob/master/docs/Install-cloudflare-wildcard-issuer.md
