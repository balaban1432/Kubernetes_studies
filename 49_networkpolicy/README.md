# Network Policy

## Minikube

* Minikube'u calico plug-in'iyle başlat

```
$ minikube start --cpus 4 --memory 6144 --cni=calico --container-runtime=docker --host-only-cidr=172.17.17.1/24
```
