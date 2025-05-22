# Домашнее задание к занятию «Helm»

## Задание 1.  Подготовить Helm-чарт для приложения

* Устанавливаю HELM:
```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
* Подготовил чарт для нашего приложения:

[netology-chart](https://github.com/A-Tagir/kubernetes/tree/main/10/netology-chart)

* Устанавливаем чарт и проверяем:

![Helm_OK](https://github.com/A-Tagir/kubernetes/blob/main/10/Kubernetes10-Helm_OK.png)

* Далее проверяем версию nginx и видим что это latest

![nginx-latest](https://github.com/A-Tagir/kubernetes/blob/main/10/Kubernetes10-Helm_nginx_latest.png)

* Меняем версию nginx на 1.22 версию чарта с 1.0.1 на 2.0.1 и применяем:
```
helm upgrade netology-chart .
Release "netology-chart" has been upgraded. Happy Helming!
NAME: netology-chart
LAST DEPLOYED: Thu May 22 19:01:22 2025
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
tiger@VM1:~/Kubernetes/10/netology-chart$ kubectl get pod
NAME                                       READY   STATUS              RESTARTS   AGE
netology-chart-backend-85999f45bb-9rg64    1/1     Running             0          2m31s
netology-chart-frontend-6b64f7d7bb-9fmks   1/1     Running             0          2m31s
netology-chart-frontend-6b64f7d7bb-lsl5q   1/1     Running             0          2m31s
netology-chart-frontend-6b64f7d7bb-qg7dn   0/1     Completed           0          2m31s
netology-chart-frontend-7894677fb6-t24fp   1/1     Running             0          3s
netology-chart-frontend-7894677fb6-xvq94   0/1     ContainerCreating   0          1s
tiger@VM1:~/Kubernetes/10/netology-chart$ kubectl get pod
NAME                                       READY   STATUS    RESTARTS   AGE
netology-chart-backend-85999f45bb-9rg64    1/1     Running   0          2m36s
netology-chart-frontend-7894677fb6-t24fp   1/1     Running   0          8s
netology-chart-frontend-7894677fb6-xvq94   1/1     Running   0          6s
netology-chart-frontend-7894677fb6-zdwt4   1/1     Running   0          4s

 helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                 APP VERSION
netology-chart  default         2               2025-05-22 19:01:22.703615783 +0300 MSK deployed        netology-chart-2.1.0  1.16.0
```
* Проверяем версию nginx и видим, что теперь 1.22:

![nginx-1.22](https://github.com/A-Tagir/kubernetes/blob/main/10/Kubernetes10-Helm_nginx_1_22.png)

## Задание 2: Запустить две версии в разных неймспейсах

* 