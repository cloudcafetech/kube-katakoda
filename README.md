## Demo Setup

Start Katakoda Kubernetes environment

https://www.katacoda.com/courses/kubernetes/getting-started-with-kubeadm

#### Demo setup for monitring and logging.

- Download below files

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/kubemon.yaml

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/kubelog.yaml

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/loki.yaml

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/loki-ds.json

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/pod-monitoring.json

wget https://raw.githubusercontent.com/cloudcafetech/kube-katakoda/master/kube-monitoring-overview.json

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
curl -vvv http://admin:admin2675@$HIP:30000/api/datasources -X POST -d @loki-ds.json -H 'Content-Type: application/json' 
```
