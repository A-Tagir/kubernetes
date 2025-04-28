# Домашнее задание к занятию «Запуск приложений в K8S»

## Задание 1. Создать Deployment и обеспечить доступ к репликам приложения из другого Pod

* microK8S и kubectl установлены
* создаю манифест ngnix-deploy.yaml:
  [nginx-deploy.yaml](https://github.com/A-Tagir/kubernetes/blob/main/03/nginx-deploy.yaml)
* Применяем: kubectl apply -f nginx-deploy.yaml
* Проверяем и видим, что запустился 1 контейнер из двух:
![pods_error](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_pod_error.png)
* Проверяем логи и видим, что nginx запустился, а multitool нет, поскольку возник конфликт портов:
![multitool_error](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_multitool_error.png)
* Правим манифест, в секцию multitool добавляем переопределение порта по-умолчанию 80->8080:
```
env:
          - name: HTTP_PORT
            value: "8080"
```
* Применяем: kubectl apply -f nginx-deploy.yaml
* Проверяем и видим, что оба контейнера запустились, реплика одна.
![pods_ok](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_pods_ok.png)
* Согласно заданию, увеличиваем количество реплик до 2-х:  replicas: 2
* Проверяем и видим, что реплик стало две, успешно запущены:
![replicas_2](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_replicas_2.png)
* Согласно заданию, создаем сервис
[nginx-service](https://github.com/A-Tagir/kubernetes/blob/main/03/nginx-service.yaml)
* Применяем и проверяем, сервис запустился. Nginx порт 8080, multitool 8090
![service_ok](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_Service_ok.png)
* Согласно заданию создаем отдельный pod multitool:
[multitool.yaml](https://github.com/A-Tagir/kubernetes/blob/main/03/multitool.yaml)
* Применяем: 
![pod_multitool_ok](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_pod_multitool.png)
* Заходим в созданный pod: kubectl exec -it pod-multitool -- /bin/bash
  ![access_ok](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_access_ok.png)

## Задание 2. Создать Deployment и обеспечить старт основного контейнера при выполнении условий

* Создаем deployment: 
[nginx-deploy-with-check.yaml](https://github.com/A-Tagir/kubernetes/blob/main/03/nginx-deploy-with-check.yaml)

Здесь нужно отметить, что в проверке доступности сервиса должно быть полное доменное имя netology-nginx.default.svc.cluster.local, иначе в некоторых контейнерах не будет работать.
* Запускаем  kubectl apply -f nginx-deploy-with-check.yaml
* Проверяем

![Pending](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_Deploy_Pending.png)

* Запускаем сервис: kubectl apply -f nginx-service.yaml
* Проверяем:

![Ready](https://github.com/A-Tagir/kubernetes/blob/main/03/Kubernetes01_Deploy_Ready.png)

