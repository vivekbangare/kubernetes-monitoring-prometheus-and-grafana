# Kubernetes monitoring - Prometheus and Grafana

Steps:

```
minikube start --memory=4098 --driver=hyperkit
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install prometheus prometheus-community/prometheus -n monitoring
helm install grafana grafana/grafana -n monitoring
```


```
k get pods -n monitoring
NAME                                                 READY   STATUS    RESTARTS   AGE
prometheus-alertmanager-0                            1/1     Running   0          51s
prometheus-kube-state-metrics-5b4ddc9578-whj59       1/1     Running   0          51s
prometheus-prometheus-node-exporter-wsr9s            1/1     Running   0          51s
prometheus-prometheus-pushgateway-85c87b49f5-fj7lr   1/1     Running   0          51s
prometheus-server-d94d88ff4-vsjd2                    2/2     Running   0          51s
```

```
$ k get svc -n monitoring
NAME                                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
prometheus-alertmanager               ClusterIP   10.106.101.247   <none>        9093/TCP   59s
prometheus-alertmanager-headless      ClusterIP   None             <none>        9093/TCP   59s
prometheus-kube-state-metrics         ClusterIP   10.96.177.107    <none>        8080/TCP   59s
prometheus-prometheus-node-exporter   ClusterIP   10.101.200.222   <none>        9100/TCP   59s
prometheus-prometheus-pushgateway     ClusterIP   10.103.74.50     <none>        9091/TCP   59s
prometheus-server                     ClusterIP   10.99.130.194    <none>        80/TCP     59s
```

```
k expose svc prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-external -n monitoring
service/prometheus-server-external exposed

k get svc -n monitoring 
NAME                                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
prometheus-alertmanager               ClusterIP   10.106.101.247   <none>        9093/TCP       7m5s
prometheus-alertmanager-headless      ClusterIP   None             <none>        9093/TCP       7m5s
prometheus-kube-state-metrics         ClusterIP   10.96.177.107    <none>        8080/TCP       7m5s
prometheus-prometheus-node-exporter   ClusterIP   10.101.200.222   <none>        9100/TCP       7m5s
prometheus-prometheus-pushgateway     ClusterIP   10.103.74.50     <none>        9091/TCP       7m5s
prometheus-server                     ClusterIP   10.99.130.194    <none>        80/TCP         7m5s
prometheus-server-external            NodePort    10.96.234.153    <none>        80:31389/TCP   38s
```

```
$ minikube ip
192.168.64.4
```

```
helm install grafana grafana/grafana -n monitoring

kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
eksV5uPUTRsEldX7piSNjpWehtM7Oj8qcjGcpiMH

k expose svc -n monitoring grafana --type=NodePort --target-port=3000 --name=grafana-external
service/grafana-external exposed
k get svc -n monitoring 
NAME                                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
grafana                               ClusterIP   10.106.151.231   <none>        80/TCP         3m14s
grafana-external                      NodePort    10.110.120.78    <none>        80:30416/TCP   16s
prometheus-alertmanager               ClusterIP   10.106.101.247   <none>        9093/TCP       14m
prometheus-alertmanager-headless      ClusterIP   None             <none>        9093/TCP       14m
prometheus-kube-state-metrics         ClusterIP   10.96.177.107    <none>        8080/TCP       14m
prometheus-prometheus-node-exporter   ClusterIP   10.101.200.222   <none>        9100/TCP       14m
prometheus-prometheus-pushgateway     ClusterIP   10.103.74.50     <none>        9091/TCP       14m
prometheus-server                     ClusterIP   10.99.130.194    <none>        80/TCP         14m
prometheus-server-external            NodePort    10.96.234.153    <none>        80:31389/TCP   7m58s
```