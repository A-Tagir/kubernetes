# Домашнее задание к занятию «Troubleshooting»

## Задание 1.  При деплое приложение web-consumer не может подключиться к auth-db. Необходимо это исправить.

* Запускаем команду согласно заданию:
```
tiger@VM1:~$ kubectl apply -f https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml
Error from server (NotFound): error when creating "https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml": namespaces "web" not found
Error from server (NotFound): error when creating "https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml": namespaces "data" not found
Error from server (NotFound): error when creating "https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml": namespaces "data" not found
```
* Создаем необходимые namespaces и снова запускаем:
```
tiger@VM1:~$  kubectl create namespace web
namespace/web created
tiger@VM1:~$  kubectl create namespace data
namespace/data created
tiger@VM1:~$ kubectl apply -f https://raw.githubusercontent.com/netology-code/kuber-homeworks/main/3.5/files/task.yaml
deployment.apps/web-consumer created
deployment.apps/auth-db created
service/auth-db created
```
* Теперь проверяем:
```
 get pods -A
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
data          auth-db-fb7c59b7f-qdflq                      1/1     Running   0          11m
ingress       nginx-ingress-microk8s-controller-qmqg2      1/1     Running   27         43d
kube-system   calico-kube-controllers-5947598c79-qr998     1/1     Running   31         52d
kube-system   calico-node-6d8sk                            1/1     Running   31         52d
kube-system   coredns-79b94494c7-hmvqr                     1/1     Running   31         52d
kube-system   csi-nfs-controller-57bcdf946b-88qrk          5/5     Running   183        33d
kube-system   csi-nfs-node-d7f8x                           3/3     Running   77         33d
kube-system   dashboard-metrics-scraper-5bd45c9dd6-vfkhw   1/1     Running   30         51d
kube-system   kubernetes-dashboard-57bc5f89fb-4hrrp        1/1     Running   29         51d
kube-system   metrics-server-7dbd8b5cc9-5rp7b              1/1     Running   39         52d
web           web-consumer-5f5b45b976-dp6pl                1/1     Running   0          11m
web           web-consumer-5f5b45b976-qbckg                1/1     Running   0          11m
```
* Еще диагностика:
```
tiger@VM1:~$ kubectl logs web-consumer-5f5b45b976-dp6pl
error: error from server (NotFound): pods "web-consumer-5f5b45b976-dp6pl" not found in namespace "default"
tiger@VM1:~$ kubectl logs web-consumer-5f5b45b976-dp6pl -n web
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
curl: (6) Couldn't resolve host 'auth-db'
```
* Здесь мы видим, что приложение web-consumer не может распознать имя auth-db. Вероятно,
  причина в том, что они в разных namespaces. 
* Deepseek нам говорит, что В Kubernetes приложения из разных неймспейсов (namespaces) по умолчанию не видят друг друга напрямую. Однако их взаимодействие можно настроить явно.

* Доступность сервисов между неймспейсами
Если у вас есть сервис (Service) в одном неймспейсе, то приложение из другого неймспейса может обратиться к нему, используя полное доменное имя (FQDN) сервиса.
* добавляем сервис:
[service.yaml](https://github.com/A-Tagir/kubernetes/blob/main/15/service.yaml)
* Применяем и видим, что связность появилась:
![auth_ok](https://github.com/A-Tagir/kubernetes/blob/main/15/Kubernetes15_auth_db_ok.png)
