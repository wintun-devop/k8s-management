### master node
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

### worker node
```
sudo apt update && sudo apt install -y wget curl make unzip network-manager gcc net-tools lsb-release ca-certificates apt-transport-https gnupg2 software-properties-common
```
