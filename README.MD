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


