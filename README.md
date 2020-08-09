# Kubernetes Platform
Vanila Kubernetes Platform with Monitoring, Logging & Backup

https://www.katacoda.com/courses/kubernetes/getting-started-with-kubeadm

## Prepare ALL Servers for Kubernetes (K8s)
OS ```CentOS 7``` to be ready before hand to start kubernetes deployment using kubeadm

### Setup Kubernetes

On Master host run following command
```
curl -LO https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/host-setup.sh; chmod +x ./host-setup.sh
./host-setup.sh master
```

On Node host run following command
```
curl -LO https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/host-setup.sh; chmod +x ./host-setup.sh
./host-setup.sh node
```

#### Setup for monitring and logging.

- Download below files

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/kubemon.yaml

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/kubelog.yaml

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/loki.yaml

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/loki-ds.json

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/pod-monitoring.json

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/kube-monitoring-overview.json

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/cluster-cost.json

- Install monitoring

```
kubectl create ns monitoring
kubectl create -f kubemon.yaml -n monitoring
```

- Install logging

```
kubectl create ns logging
kubectl create secret generic loki -n logging --from-file=loki.yaml
kubectl create -f kubelog.yaml -n logging
```

- Upload Grafana dashboard & loki datasource

```
HIP=`ip -o -4 addr list eth0 | awk '{print $4}' | cut -d/ -f1`
curl -vvv http://admin:admin2675@$HIP:30000/api/dashboards/db -X POST -d @pod-monitoring.json -H 'Content-Type: application/json'
curl -vvv http://admin:admin2675@$HIP:30000/api/dashboards/db -X POST -d @kube-monitoring-overview.json -H 'Content-Type: application/json'
curl -vvv http://admin:admin2675@$HIP:30000/api/dashboards/db -X POST -d @cluster-cost.json -H 'Content-Type: application/json'
curl -vvv http://admin:admin2675@$HIP:30000/api/datasources -X POST -d @loki-ds.json -H 'Content-Type: application/json' 
```
