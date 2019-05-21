## Setup RKE (Rancer Kubernetes Engine) cluster

If you are not prepared `RKE` enviroment yet, follow [this installation guid](RKE_PREPARATION.md) for prepration. Before we start to setup `RKE` cluster, please check `ip` address on all nodes.

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
2. Run `rke up` commend to start the cluster. It will to take too long to base on your internet connection.
```
$ rke up --config rancher-cluster.yaml
```
If you get the message `Finished building Kubernetes cluster successfully` as below, you are successfully setup the `kubernetes` cluster.
![rke](/rke.png)

3. Set the `KUBECONFIG` environmental variable to the path of `kube_config_rancher-cluster.yml` which created by RKE installation.
```
export KUBECONFIG=$(pwd)/kube_config_rancher-cluster.yml
```
