### k8s install
- https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#download-binary-linux-0
```
sudo apt update && sudo apt install -y wget curl make unzip network-manager gcc net-tools lsb-release ca-certificates apt-transport-https gnupg2 software-properties-common
```
```
sudo curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes.gpg
```
```
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```
sudo apt update
```
```
sudo apt install -y kubelet kubeadm kubectl
```
```
sudo apt-mark hold kubelet kubeadm kubectl

```
