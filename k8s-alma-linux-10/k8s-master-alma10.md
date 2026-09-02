### k8s master System Prerequisites
- Disable swap
```
sudo swapoff -a
```
```
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```
- SELinux permissive
```
sudo setenforce 0
```
```
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```
-  Load kernel modules
```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```
```
sudo modprobe overlay
```
```
sudo modprobe br_netfilter
```
- Sysctl settings
```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```
```
sudo sysctl --system
```
### Install Container Runtime (containerd)
```
sudo dnf install -y containerd
```
```
sudo mkdir -p /etc/containerd
```
```
containerd config default | sudo tee /etc/containerd/config.toml
```
```
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```
```
sudo systemctl restart containerd
```
```
sudo systemctl enable containerd
```

### Install Kubernetes Packages
```
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.36/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.36/rpm/repodata/repomd.xml.key
EOF
```
```
sudo dnf install -y kubelet kubeadm kubectl
```
```
sudo systemctl enable kubelet
```
### Initialize the Master Node
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket unix:///run/containerd/containerd.sock
```
### Configure kubectl for Admin User
```
mkdir -p $HOME/.kube
```
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### Install a CNI Plugin (Calico or Flannel)
- Calico
```
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/calico.yaml
```
- To install the Cilium driver (CNI plugin) on your AlmaLinux Kubernetes cluster
```
sudo modprobe bpfilter
```
```
sudo modprobe br_netfilter
```
```
sudo sysctl -w net.ipv4.ip_forward=1
```
```
sudo sysctl -w net.bridge.bridge-nf-call-iptables=1
```
```
cat <<EOF | sudo tee /etc/sysctl.d/99-cilium.conf
net.ipv4.ip_forward=1
net.bridge.bridge-nf-call-iptables=1
EOF
```
```
sudo sysctl --system
```
```
CILIUM_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
```
```
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_VERSION}/cilium-linux-amd64.tar.gz{,.sha256sum}
```
```
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum
```
```
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
```
```
rm cilium-linux-amd64.tar.gz*
```
```
cilium version
```
```
cilium install --version 1.15.1 --set kubeProxyReplacement=true
```
- Verify Installation
```
cilium status
```
```
kubectl -n kube-system get pods -l k8s-app=cilium

```
- Verify Master Node    
```
kubectl get nodes -o wide
```
kubectl get pods -n kube-system
```
- Connectivity Test
```
cilium connectivity test
```