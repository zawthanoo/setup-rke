## Setup Helm on CentOs-7
1.Get `helm` package
```
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.12.3-linux-amd64.tar.gz
```
2.Unzip downloaded file
```
tar -zxvf helm-v2.12.3-linux-amd64.tar.gz
```
3.Move `helm` file to `/usr/bin` directory
```
mv linux-amd64/helm /usr/bin/helm
```
4.Check `helm` is running or not
```
helm version
```
