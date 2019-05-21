## Preparation for HA Kubernets Cluster(RKE) on local PC?
1. Install Docker on all nodes
2. Install Kubectl on all nodes and master
3. Install RKE on master.
4. Setup SSH tunneling rancher to all nodes.
5. Open firewall ports as a services for all nodes and rancher.
 
Create four CentOs-7 VM :
1. Rancher : CPU 2 cores, RAM 4G, HD 40G
2. Node1/Kube1 : CPU 2 cores, RAM 4G, HD 40G
3. Node2/Kube2 : CPU 2 cores, RAM 4G, HD 40G
4. Node3/Kube3 : CPU 2 cores, RAM 4G, HD 40G

### 1. Install Docker on all nodes(Node1, Node2, Node3)
1.First, `yum` update.
 ```
$ yum update
 ```
2.Install required packages
```
$ yum install -y yum-utils device-mapper-persistent-data lvm2
```
3.Setup the stable docker repository.
```
$ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
4.Check docker version
```
$ yum list docker-ce
<OR>    
$ yum list docker-ce --showduplicates | sort -r
```
4.Install docker specific version. Currently, Rancher2 only support max docker version `17.03.xx`.
```
$ yum install docker-ce-selinux-17.03.2.ce-1.el7.centos
$ yum install docker-ce-17.03.2.ce-1.el7.centos
```
5.Enable `docker` service on boot level start.
```
$ systemctl enable docker
```
6.Start `docker` service
```
$ systemctl start docker 
```
7.Check `docker` service is running or not
```
$ docker version
$ docker ps
```
![Docker](/docker.png)

8.Add a new user for docker and give permission
```
$ adduser dockeruser
$ passwd dockeruser
$ groupadd docker
$ usermod -aG docker dockeruser
$ chown "dockeruser":"docker" /etc/docker/.docker -R
$ chmod g+rwx "/etc/docker/.docker" -R
```

### 2. Install kubectl on all nodes and rancher(Node1, Node2, Node3, Rancher)
1.Install `kubectl`
```
$ cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
$ yum install -y kubectl
```

### 3. Install RKE (Rancher Kubernetes Engine) on Rancher

1.Download `RKE`.
```
$ wget https://github.com/rancher/rke/releases/download/v0.1.15/rke_linux-amd64
```
2.Move to `bin` directory.
```
$ mv rke_linux-amd64 /usr/bin/rke
```
3.Make Exacuteable
```
$ chmod +x /usr/bin/rke
```
4.Check version , whether it is OK or not.
```
$ rke --version
```
### 4. Setup SSH tunneling rancher to all nodes.
Clease check `ip` address on all nodes and rancher.

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

1.Run `ssh-keygen` on Rancher.
```
$ ssh-keygen
```
2.Copy `ssh-id` to all nodes.
```
$ ssh-copy-id dockeruser@192.168.123.9
$ ssh-copy-id dockeruser@192.168.123.10
$ ssh-copy-id dockeruser@192.168.123.11
```
### 5. Open firewall ports as a services for all nodes and rancher.
1.Create `firewall-config` file for all nodes with name `node-firewall.xml`.
```
$ vi node-firewall.xml
```
node-firewall.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<service>
	<port port="2376" protocol="tcp"/>
	<port port="2379" protocol="tcp"/>
	<port port="2380" protocol="tcp"/>
	<port port="8472" protocol="udp"/>
	<port port="9099" protocol="tcp"/>
	<port port="10250" protocol="tcp"/>
	<port port="443" protocol="tcp"/>
	<port port="6443" protocol="tcp"/>
	<port port="8472" protocol="udp"/>
	<port port="6443" protocol="tcp"/>
	<port port="10254" protocol="tcp"/>
	<port port="30000-32767" protocol="tcp"/>
</service>
```
Run the following command
```
$ firewall-offline-cmd --new-service-from-file=rancher-firewall.xml --name=rancher-firewall
$ firewall-cmd --reload
$ firewall-cmd --add-service rancher-firewall
```
2.Create `firewall-config` file for all rancher with name `rancher-firewall.xml`.
```
$ vi rancher-firewall.xml
```
rancher-firewall.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<service>
	<port port="80" protocol="tcp"/>
	<port port="433" protocol="tcp"/>
	<port port="22" protocol="tcp"/>
	<port port="2376" protocol="tcp"/>
	<port port="6443" protocol="tcp"/>
</service>
```
Run the following command
```
$ firewall-offline-cmd --new-service-from-file=rancher-firewall.xml --name=rancher-firewall
$ firewall-cmd --reload
$ firewall-cmd --add-service rancher-firewall
```
3.If you don't want to do `step-1` and `step-2`, you can disable `firewall` service.
```
$ systemctl stop firewalld
$ systemctl disable firewalld
$ systemctl status firewalld
```
