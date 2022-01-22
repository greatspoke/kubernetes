## To start using ingress-nginx-controller on K8s cluster

##### First we need create a namespace in your kubernetes cluster:
```
create ns ingress-nginx
```

##### Use this line to install the additional instance of the ingress controller:
```
kubectl -n ingress-nginx apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml
```


##### Then we need patch your ingres and setup your remote ip to your ingress-nginx-controller:
```
patch svc ingress-nginx-controller -n ingress-nginx -p '{"spec": {"type": "LoadBalancer", "externalIPs":["<your remote ip>"]}}'
```



`Done! ingres-nginx controller installed successfully!`

# For next step need to install [Certificate-and-Ingress] 

[Certificate-and-Ingress]: https://github.com/greatspoke/kubernetes/blob/master/docs/Install-certificate-and-ingress.md