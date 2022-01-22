## To start using K8s on Ubuntu 20.04

##### Login as `root` user
```
sudo su -
```
Perform all the commands as root user unless otherwise specified
##### Disable Firewall
```
ufw disable
```
##### Disable swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```
##### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
##### Install docker engine
```
apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
apt update
apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io

```
### Kubernetes Setup
##### Add Apt repository
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
```

##### Install Kubernetes components
```
apt update && apt install -y kubeadm=1.19.0-00 kubelet=1.19.0-00 kubectl=1.19.0-00
```
##### In case you are using LXC containers for Kubernetes nodes
Hack required to provision K8s v1.15+ in LXC containers
```
mknod /dev/kmsg c 1 11
echo '#!/bin/sh -e' >> /etc/rc.local
echo 'mknod /dev/kmsg c 1 11' >> /etc/rc.local
chmod +x /etc/rc.local
```
##### Initialize Kubernetes Cluster
Update the below command with the ip address of your remote machine
```
kubeadm init --apiserver-advertise-address=<remote ip address> --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
```
##### Deploy Calico network
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```

##### To be able to run kubectl commands as non-root user
If you want to be able to run kubectl commands as non-root user, then as a non-root user perform these
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```


Thats all! `Happy Kuberneting`!!

# For next step need to install [Helm] 

[Helm]: https://github.com/greatspoke/kubernetes/blob/master/docs/Install-helm-on-local-machine.md

