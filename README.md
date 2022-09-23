# k3s-lab-victoriametrics
victoriametrics 简称 VM
victoriametrics lab with k3s

## 架构图
https://docs.qq.com/doc/DWFRRUUJEUWJwQ1dt

## Requirements


### Install docker desktop

https://docs.docker.com/desktop/install/mac-install/
https://docs.docker.com/desktop/install/windows-install/

资源开大一点，建议 3c 6g +

### Install k3d
https://github.com/k3s-io/k3s 
https://github.com/k3d-io/k3d 

k3s for docker, 在容器内起k8s，用于测试、CI等场景


```bash
# or install with homebrew, macos only
brew install k3d
```

### Start k8s cluster

```
k3d cluster create victoriametrics
```

### Install flux
https://github.com/fluxcd/flux2
为了更声明式的方式管理 helm release/repo，取代 `helm repo` `helm install`等命令

```
flux bootstrap github --owner=repoOrg --repository=repoName --path=./flux2/clusters/k3d-victoriametrics --personal --token-auth

```
## Install victoriametrics

helm repo: https://github.com/VictoriaMetrics/helm-charts

```bash
kubectl apply -k k3d-victoriametrics/victoriametrics
kubectl apply -k k3d-victoriametrics/alertmanager
kubectl apply -k k3d-victoriametrics/prometheusalert
```

## 访问

### 暴露 k3d lb 为8080端口

```
k3d node edit k3d-victoriametrics-serverlb  --port-add 6080:80
```

### 绑hosts

```
127.0.0.1 vmagent.example.com grafana.example.com vmstorage.example.com vmselect.example.com vminsert.example.com vmalert.example.com alertmanager.example.com prometheusalert.example.com
```

### 访问

http://grafana.example.com:6080

http://alertmanager.example.com:6080
http://vmalert.example.com:6080
http://prometheusalert.example.com:6080

http://vmagent.example.com:6080
http://vmstorage.example.com:6080
http://vmselect.example.com:6080
http://vminsert.example.com:6080


## 关键点

1. crd `kubectl get crd | grep victoria`
2. flux2的helm controller, 见上面，可选，但是利于使用gitops
3. kustmization  `kubectl apply -k`


## Doc & FAQ

https://docs.victoriametrics.com/FAQ.html

## prometheus vs victorimetrics

VM作者博客： https://valyala.medium.com/

benchmark Prom vs VM: https://valyala.medium.com/prometheus-vs-victoriametrics-benchmark-on-node-exporter-metrics-4ca29c75590f

Thanos vs VM: https://faun.pub/comparing-thanos-to-victoriametrics-cluster-b193bea1683

历史 ：https://faun.pub/victoriametrics-creating-the-best-remote-storage-for-prometheus-5d92d66787ac