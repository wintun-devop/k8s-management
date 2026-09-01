### install cilium
```
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
```
```
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-amd64.tar.gz{,.sha256sum}
```
```
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum
```
```
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
```
```
rm cilium-linux-amd64.tar.gz{,.sha256sum}
```
```
cilium version --client
```
### Remove Any Existing CNI
```
kubectl delete -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```
```
sudo rm -rf /etc/cni/net.d/*
```
```
sudo rm -rf /opt/cni/bin/*
```
### install cilium driver
```
cilium install --version 1.20.1 --set kubeProxyReplacement=true
```
```
sudo systemctl restart containerd
```
```
sudo systemctl restart kubelet
```

