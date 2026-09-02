### master node
```
sudo apt update && sudo apt install -y wget curl make unzip network-manager gcc net-tools lsb-release ca-certificates apt-transport-https gnupg2 software-properties-common gpg
```
- ubuntu static ip
```
ls /etc/netplan/
```
```
sudo cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.yaml.bak
```
```
sudo vi /etc/netplan/50-cloud-init.yaml
```
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses: [192.168.1.85/24]
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```
```
sudo netplan apply
```
- Disable swap (Kubernetes requires swap off)
```
sudo swapoff -a
```
```
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

- Load required kernel modules
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

- Set sysctl params
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
- containerd installed
```
sudo apt update -y
```
```
sudo apt upgrade -y
```
```
sudo apt install -y containerd
```
- Generate default config
```
sudo mkdir -p /etc/containerd
```
```
sudo containerd config default | sudo tee /etc/containerd/config.toml
```
- Use systemd cgroup driver
```
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```
- Restart containerd
```
sudo systemctl restart containerd
```
```
sudo systemctl enable containerd
```
- Add Kubernetes apt repo
```
sudo mkdir -p /etc/apt/keyrings
```
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
```
```
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.36/deb/ /" | \
```
```
sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```
sudo apt update
```
- Install kubelet, kubeadm, kubectl
```
sudo apt install -y kubelet kubeadm kubectl
```
```
sudo apt-mark hold kubelet kubeadm kubectl
```
- run on master node
```
sudo kubeadm reset -f
```
```
sudo kubeadm init --pod-network-cidr=10.111.0.0/16 --cri-socket unix:///run/containerd/containerd.sock
```
```
mkdir -p $HOME/.kube
```
```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### worker node
```
sudo apt update && sudo apt install -y wget curl make unzip network-manager gcc net-tools lsb-release ca-certificates apt-transport-https gnupg2 software-properties-common
```
- ubuntu static ip
```
ls /etc/netplan/
```
```
sudo cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.yaml.bak
```
```
sudo vi /etc/netplan/50-cloud-init.yaml
```
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: false
      addresses: [192.168.1.85/24]
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
```
```
sudo netplan apply
```
- Disable swap (Kubernetes requires swap off)
```
sudo swapoff -a
```
```
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

- Load required kernel modules
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

- Set sysctl params
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
- containerd installed
```
sudo apt update -y
```
```
sudo apt upgrade -y
```
```
sudo apt install -y containerd
```
- Generate default config
```
sudo mkdir -p /etc/containerd
```
```
sudo containerd config default | sudo tee /etc/containerd/config.toml
```
- Use systemd cgroup driver
```
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```
- Restart containerd
```
sudo systemctl restart containerd
```
```
sudo systemctl enable containerd
```
- Add Kubernetes apt repo
```
sudo mkdir -p /etc/apt/keyrings
```
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | \
```
```
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.36/deb/ /" | \
```
```
sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```
sudo apt update
```
- Install kubelet, kubeadm, kubectl
```
sudo apt install -y kubelet kubeadm kubectl
```
```
sudo apt-mark hold kubelet kubeadm kubectl
```
- To connect k8s worker to master,create token on master
```
kubeadm token create --print-join-command
```
- join to master
- (192.168.1.85:6443 is master node ip)
```
sudo kubeadm join 192.168.1.85:6443 --token <TOKEN> \
    --discovery-token-ca-cert-hash sha256:<HASH> \
    --cri-socket unix:///run/containerd/containerd.sock
```
```
sudo kubeadm join 192.168.1.85:6443 --token tg4jyv.0faxrmt9qdq7ylbq \
--discovery-token-ca-cert-hash sha256:a22752d5dc63e0d2d1fa2015874565ce557b43979919c7e2587b412f5a837b6e \
--cri-socket unix:///run/containerd/containerd.sock
```

### Fixed Swap Issue on worker node
```
sudo swapoff -a
```
```
sudo sed -i '/swap.img/s/^/#/' /etc/fstab
```
```
cat /etc/fstab | grep swap
```
```
sudo systemctl restart kubelet
```
```
sudo systemctl status kubelet
```


