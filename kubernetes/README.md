# Kubernetes notes
Some notes on kubernetes setup on Ubuntu 18.04.2 LTS servers.

## How to setup kubernetes cluster
This 10-minute how-to is crafted for Ubuntu Server 18.04.2 LTS using [this](https://www.linuxtechi.com/install-configure-kubernetes-ubuntu-18-04-ubuntu-18-10/) and
[this](https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network) manuals.

### Network topology
Assuming master kubernes node at 192.168.56.101 and workers at 192.168.56.102, 192.168.56.103, ...
All nodes are visible amongst each other.

### On all nodes
This is basic node setup for all kubernetes cluster nodes (master and all workers).
Assuming bare Ubuntu Server 18.04.2 LTS was installed with ssh server.
```
sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo apt-get install apt-transport-https curl -y
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
sudo swapoff -a	
#disable swap by editing /etc/fstab, it remains disabled abter reboot
sudo apt-get install kubeadm -y
```

### On master node
Init kubernetes master node, use flannel networking:
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
Last __kubeadm init__ command prints out token and token CA cert hash. Save those for worker node setup.

As regular user, finish the setup:
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/a70459be0084506e4ec919aa1c114638878db11b/Documentation/kube-flannel.yml
```

### Attach worker nodes
On each worker node, run __kubeadm join__ like example below. You will get exact token values from previous run of __kubeadm init__ on master node.
```
sudo kubeadm join 192.168.56.101:6443 --token zvrxax.gfhprvpi8bioefg1 --discovery-token-ca-cert-hash sha256:01efb4c09278a75ec2df7472e6754ad0d8ce1756730acb40b8c89ab03425a31d
```

### Check cluster status
Check if nodes (cluster is running properly) on master.
```
sudo kubectl get nodes
sudo kubectl get pods --all-namespaces
```

## Single node Kubernetes
[microk8s](https://microk8s.io/) [docs](https://microk8s.io/docs/)
```
snap info microk8s
sudo microk8s.stop
sudo microk8s.start
```

