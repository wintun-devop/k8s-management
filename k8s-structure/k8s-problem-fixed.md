### Fix on kubectl error Worker Node
- By default, kubectl looks for a kubeconfig file in $HOME/.kube/config
- On worker nodes, that file often doesn’t exist unless you copy it from the master.
- Without a kubeconfig, kubectl falls back to http://localhost:8080, which fails.
- On master node
```
mkdir -p $HOME/.kube
```
```
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```
chown $(id -u):$(id -g) $HOME/.kube/config
```
- Copy to worker node (replace with your worker IP)
```
scp $HOME/.kube/config sysadmin@192.168.1.86:/home/sysadmin/.kube/config
```
- On the worker
```
mkdir -p $HOME/.kube
```
- (after scp, ensure ownership)
```
chown $(id -u):$(id -g) $HOME/.kube/config
```
