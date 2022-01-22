## To start using helm on local machine


##### Installing Helm From Script
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
##### From Homebrew (macOS)
```
brew install helm
```

##### From Chocolatey (Windows)
```
choco install kubernetes-helm
```


##### From Apt (Debian/Ubuntu)
```
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

##### From Snap
```
sudo snap install helm --classic

```


# For next step need to install [Cert-Manager] 

[Cert-Manager]: https://github.com/greatspoke/kubernetes/blob/master/docs/Install-kubernetes-cert-manager.md