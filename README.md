## Setup RKE (Rancher Kubernetes Engine) cluster

If `RKE` environment is prepared yet, please follow [this installation guid](RKE_PREPARATION.md). Before setup the `RKE` cluster, please check `ip` address on all nodes.

### Setup RKE Cluster on Rancher Node 
1. create a cluster config file, name as `rancher-cluster.yaml`.
```
vi rancher-cluster.yaml
```
rancher-cluster.yaml
```yaml
nodes:
  - address: 192.168.123.9
    user: dockeruser
    role: [controlplane, etcd, worker]
  - address: 192.168.123.10
    user: dockeruser
    role: [controlplane, etcd, worker]
  - address: 192.168.123.11
    user: dockeruser
    role: [controlplane, etcd, worker]

services:
  etcd:
    snapshot: true
    backup: false
    creation: 6h
    retention: 24h
```
2. Run `rke up` commend to start the cluster. It will to take too long to base on internet connection and hardware resources.
```
$ rke up --config rancher-cluster.yaml
```
If the message `Finished building Kubernetes cluster successfully` is got, the `kubernetes` cluster is successfully setup.
![rke](/rke.png)

3. Set the `KUBECONFIG` environmental variable to the path of `kube_config_rancher-cluster.yaml` which created by `RKE` installation.
```
export KUBECONFIG=$(pwd)/kube_config_rancher-cluster.yaml
```
4. Check all of nodes status on the `RKE` cluster
```
$ kubectl get nodes
```
![nodes-status](/nodes-status.png)

5. Check all of pods status on the `RKE` cluster.
```
$ kubectl get pods --all-namespaces
```
![pods-status](/pods-status.png)

Most are `Running` and `Completed`.
![pods-status](/pods-status2.png)

# Issue
>Failed to start [rke-etcd-port-listener] container on host [192.168.123.11]: Error response from daemon: driver failed programming external connectivity on endpoint rke-etcd-port-listener (c35e1742f00f349baac3d57b968f2bf30c6b2eb82a586d4641e29f447f295d2c):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 2380 -j DNAT --to-destination 172.17.0.2:1337 ! -i docker0: iptables: No chain/target/match by that name.
 (exit status 1))

- Delete all docker image and container on all nodes.
```
$ docker system prune && docker rm $(docker ps -a -q) -f && docker rmi $(docker images -q) -f
```
- Restart docker on all nodes
```
$ systemctl restart docker
```
- Remove previous `rke` installation.
```
$ rke remove --config rancher-cluster.yaml
```
- Install `rke` agian
```
$ rke up --config rancher-cluster.yaml
```
