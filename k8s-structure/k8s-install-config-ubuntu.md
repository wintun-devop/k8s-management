### necessary packages for k8s install
```
sudo apt update && sudo apt install -y wget curl make unzip network-manager gcc net-tools lsb-release ca-certificates apt-transport-https gnupg2 software-properties-common
```
- https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#download-binary-linux-0
- https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.35/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
```
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
### k8s install
```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.35/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
```
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
```
```
sudo apt update -y
```
```
sudo apt install -y kubelet kubeadm kubectl
```
### Install Container Runtime
```
sudo apt install -y containerd
```
```
sudo mkdir -p /etc/containerd
```
```
containerd config default | sudo tee /etc/containerd/config.toml
```
```
sudo systemctl restart containerd
```
```
sudo systemctl enable containerd
```
