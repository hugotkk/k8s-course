# docker image full failed

https://stackoverflow.com/questions/49387263/docker-error-response-from-daemon-get-https-registry-1-docker-io-v2-servic

fail to pull the image when calling
```
docker run -it <container_image>
```

```
docker logout
docker login
```
To solve the issue

# cannot access the NodePort service

https://stackoverflow.com/questions/60710171/minikube-ip-is-not-reachable

use
```
minikube start --vm-drive=virtualbox
```
or 
```
minikube start --vm-drive=none
```

default is using docker. the port does not expose as well.

# Architecture

deployment -> replicaset -> pod

nodeport service -> pod

deployment ->old replicaset is still here but replicas become 0

minReadySeconds under DeploymentSpec > spec type

# Dashboard (namepsace = default)

```
$ kubectl get all
NAME                          READY   STATUS    RESTARTS   AGE
pod/queue-release-0           1/1     Running   0          60m
pod/webapp-77896f4bf8-m27j2   1/1     Running   0          11m
pod/webapp-77896f4bf8-zhp2b   1/1     Running   0          10m

NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/fleetman-queue    NodePort    10.106.232.71    <none>        8161:30010/TCP   173m
service/fleetman-webapp   NodePort    10.101.127.112   <none>        80:30080/TCP     173m
service/kubernetes        ClusterIP   10.96.0.1        <none>        443/TCP          173m

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp   2/2     2            2           49m

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-6ccdfd88cb   0         0         0       41m
replicaset.apps/webapp-77896f4bf8   2         2         2       40m
replicaset.apps/webapp-d48d5ff54    0         0         0       9m57s
```

# DNS

```
$ kubectl get ns
NAME              STATUS   AGE
default           Active   173m
kube-node-lease   Active   173m
kube-public       Active   173m
kube-system       Active   173m

$ kubectl get po -n kube-system
NAME                               READY   STATUS    RESTARTS   AGE
coredns-74ff55c5b-vrrsz            1/1     Running   0          174m
etcd-minikube                      1/1     Running   0          174m
kube-apiserver-minikube            1/1     Running   0          174m
kube-controller-manager-minikube   1/1     Running   0          174m
kube-proxy-jb7m4                   1/1     Running   0          174m
kube-scheduler-minikube            1/1     Running   0          174m
storage-provisioner                1/1     Running   1          174m
```

```
$ kubectl describe svc -n kube-system kube-dns
Name:              kube-dns
Namespace:         kube-system
Labels:            k8s-app=kube-dns
                   kubernetes.io/cluster-service=true
                   kubernetes.io/name=KubeDNS
Annotations:       prometheus.io/port: 9153
                   prometheus.io/scrape: true
Selector:          k8s-app=kube-dns
Type:              ClusterIP
IP Families:       <none>
IP:                10.96.0.10
IPs:               10.96.0.10
Port:              dns  53/UDP
TargetPort:        53/UDP
Endpoints:         172.17.0.2:53
Port:              dns-tcp  53/TCP
TargetPort:        53/TCP
Endpoints:         172.17.0.2:53
Port:              metrics  9153/TCP
TargetPort:        9153/TCP
Endpoints:         172.17.0.2:9153
Session Affinity:  None
Events:            <none>
 
- keep namespace in default first, put third parties plugin into other namespace
```

```
/ # nslookup database
nslookup: can't resolve '(null)': Name does not resolve

Name:      database
Address 1: 10.111.239.97 database.default.svc.cluster.local
```
The hostname is database.default.svc.cluster.local for the databse pod. Why database works?

Because the resolv.conf search added `default.svc.cluster.local`

```
/ # cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
```
