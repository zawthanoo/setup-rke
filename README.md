## Setup RKE (Rancer Kubernetes Engine) cluster

If you are not prepared `RKE` enviroment yet, follow [this installation guid](RKE_PREPARATION.md) for prepration. Before we start to setup `RKE` cluster, please check `ip` address on all nodes.

Example, all VM `ip` address in my PC is:
<table>
  <tr>
    <td>
      Rancher
    </td>
    <td>
      192.168.123.8
    </td>
  </tr>
  <tr>
    <td>
      Node1
    </td>
    <td>
      192.168.123.9
    </td>
  </tr>
  <tr>
    <td>
      Node2
    </td>
    <td>
      192.168.123.10
    </td>
  </tr>
  <tr>
    <td>
      Node3
    </td>
    <td>
      192.168.123.11
    </td>
  </tr>
</table>

### Setup `SSH` tunneling `rancher` to all `nodes`
Create `ssh` key
```
$ ssh-keygen
```
Copy `ssh` key to all nodes
```
$ ssh-copy-id dockeruser@192.168.123.9
$ ssh-copy-id dockeruser@192.168.123.10
$ ssh-copy-id dockeruser@192.168.123.11
```
Example:
![ssh-keygen](/ssh.png)

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


