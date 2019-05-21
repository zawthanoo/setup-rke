## Preparation for HA Kubernets Cluster(RKE) on local PC?
1. Install Docker on all nodes
2. Install Kubectl on all nodes and master
3. Install RKE on master.
 
Create four CentOs-7 VM :
1. Master/Rancher : CPU 2 cores, RAM 4G, HD 40G
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
4.Install docker specific version
```
$ yum install docker-ce.<VERSION STRING>
```
Eg: `yum install docker-ce.x86_64`

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
![Docker](/docker-snp.png)

8.Add a new user for docker and give permission
```
$ adduser dockeruser
$ passwd dockeruser
$ groupadd docker
$ usermod -aG docker dockeruser
$ chown "dockeruser":"docker" /etc/docker/.docker -R
$ chmod g+rwx "/etc/docker/.docker" -R
```

### 2. Install kubectl on all nodes and master(Node1, Node2, Node3, Master)
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

### 2. Install RKE (Rancher Kubernetes Engine) master

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


